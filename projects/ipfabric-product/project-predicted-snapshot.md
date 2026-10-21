---
name: project-predicted-snapshot
description: "Propagation engine dev state: zone model, STP/VRRP/OSPF diffs, mutation script, open gaps"
metadata:
  type: project
---

Colleague Vinny has built a tool that takes an IP Fabric snapshot TAR, modifies per-device JSON files within it, and writes a new TAR. This is the rendering layer for the propagation zone model's predicted snapshot output.

**Why:** Closes the loop on the predicted-state pipeline: the propagation model computes which zone boundaries change and what state cascades; Vinny's tool writes that predicted state back as a valid snapshot that IP Fabric can natively analyse.

**How Vinny's tool works today:**
- SLM (small language model) front end: accepts natural language instructions
- Loads snapshot TAR, modifies the target device's JSON, writes new TAR
- **Limitation: works one device at a time** — must be extended to batch for our use case

**Proposed integration API (to request from Vinny):**
- Drop the SLM layer entirely for our use case — the propagation model produces field-level mutations directly, no NL translation needed
- Accept a batch input: one input snapshot TAR + list of `{ sn, fields }` mutations → one output TAR
- `sn` = IP Fabric device serial number (primary device identifier)
- `fields` = JSON patch against the device's `msg` object (the field names mapped in llm-wiki: predicted-snapshot-mutation-model)
- Batch is essential for correctness: the predicted snapshot must reflect all changes simultaneously, not sequentially applied across N intermediate TARs

**Pipeline architecture:**
1. Read current zone state → IP Fabric API (endpoints mapped in llm-wiki: ipf-zone-api-mapping)
2. Apply proposed change → propagation zone model (biconnected components, existence-based + information-flow boundaries)
3. Compute per-device mutations → list of `{ sn, fields }` (Layer 1 + Layer 2 from llm-wiki: predicted-snapshot-mutation-model)
4. Call Vinny's batch API → one input TAR → one predicted output TAR
5. Analyse predicted snapshot → IP Fabric native capabilities (path lookup, security, reachability)
6. Diff current vs predicted → impact assessment

**How to apply:** When talking to Vinny, frame the ask as "expose the JSON patch layer underneath your NL interface as a batch API." The mechanism is already built; we need it surfaced without the SLM in front and accepting multiple devices per call.

**Key insight:** The predicted snapshot lets IP Fabric's existing analysis run against the future state — no separate impact engine needed. The current→predicted diff IS the change impact report.

**Integration contract — operation vocabulary (2026-10-14):**
Vinny's pipeline is now fully documented (`IPF Insight Center Snapshot Mutation Processes.pdf`). The model emits a typed plan of 13 operations; the application applies them. Critical constraints for our engine:

1. **Diffs must be prefix-level, not zone-level.** The mutation tool needs `remove_route` per specific prefix, `remove_arp` per specific IP, `remove_mac` per specific MAC — not "zone X was disrupted." Our current `DeviceDiff`/`TableDiff` output is zone-level approximation. Translating to prefix-level is the main integration work.
2. **`remove_route` removes by prefix — all nexthops at once.** ECMP partial-failure requires `remove_route` then `add_route` with only the surviving nexthop. The engine needs to enumerate surviving nexthops, not just identify the lost one.
3. **`set_device_status` handles DEVICE_DOWN** — plus `remove_neighbor` on peers, plus `remove_route` for every prefix that resolved via that device. No bulk "remove everything" operation exists.
4. **The validator (Stage 3) will catch incomplete plans visibly.** A plan that shuts interfaces but leaves routes pointing through them produces a validator finding. This is useful: it means wrong diffs produce a visible signal, not a silently corrupt snapshot.
5. **The LLM front-end is a separate concern.** The mutation backend (`scenario-applier.ts`) accepts a typed `ScenarioPlan` directly. Our engine bypasses the LLM and feeds the plan layer directly — no NL translation needed.

**How to apply:** When building the engine→mutation bridge, the engine's per-device diffs need to enumerate specific prefixes (from the snapshot's routing table) and specific ARP/MAC entries, not just zone membership. The snapshot fetch layer already has the routing table data; it needs to be made available to `compute_diffs()` in each zone module.

---

## Engine implementation state (as of 2026-10-21, commit f962ee6)

**Repo:** `D:\users\daren\repos\ipf-prediction-engine` (main branch, up to date)

**Zone modules implemented:** STP, OSPF, BGP, EIGRP, OMP, VXLAN, VRRP

### Current design (accumulated across sessions)

**zones/stp.py — 802.1D election and MAC walk**
- `_stp_election(exclude_device=...)`: full Dijkstra with `pred_port[v]` (v's root-port interface) and `pred_up_port[v]` (upstream's port toward v), with port-level tiebreaks for parallel links (lowest bridge_id → lowest upstream port ID → lowest local port ID). Returns 5-tuple `(root, port_roles, pred, pred_port, pred_up_port)`.
- Port role assignment uses `pred_port.get(b) == ib` to correctly distinguish root vs alternate among parallel links connecting the same pair of switches.
- `_compute_mac_diffs(vlan_id, pred, pred_port, pred_up_port, exclude_device, virtual_macs)`: SPT-based MAC re-learning after TC flush. Access anchors (non-ISL ports) drive the walk; VRRP virtual MACs excluded from anchors to avoid collision with the VRRP module. Uses `pred_port`/`pred_up_port` directly instead of an ambiguous `port_toward` dict.
- `_virtual_macs` populated in `identify_zones()` from snapshot FHRP data.

**zones/vrrp.py — FHRP failover + virtual MAC movement**
- Identifies zones by (site_id, vlan_id, group_id).
- `_compute_virtual_mac_diffs()`: `old_port` per switch read from snapshot MAC table (ground truth — avoids topology-vs-reality mismatches). `new_port` from Dijkstra from new_master (old_master excluded), using `pred_port`/`pred_up_port` with port-level tiebreaks.
- Unconditional downed-device sweep in `mutate_snapshot.py` catches FHRP rows the zone module misses (e.g. backup rows on VLANs where no election fires).

**zones/ospf.py**
- `_boundary_routes_changed()` skips the downed device as Dijkstra destination — suppresses false route_withdrawal on redundant paths.

**engine/snapshot.py**
- `_fetch_interfaces()` fetches both `ip` (host IP) and `net` (network address) fields. `_device_ips()` in `mutate_snapshot.py` uses `ip` — not `net.split("/")[0]` — for nexthop matching.
- `_fetch_fhrp()` uses `tables/fhrp/group-state`; derives virtual_mac from protocol+group.

**mutate_snapshot.py**
- Routing removals: (1) all rows on downed device, (2) rows on `affected_devices` whose `nexthop_ip` is in downed device's host IPs. Rule 3 (remove by connected-network prefix) dropped — wrong for redundant-gateway topologies where partner keeps advertising those prefixes.
- `affected_devices` extracted from all zone results — routing scope is bounded to propagation zone.
- Downed-device sweep after zone diffs: marks remaining FHRP and MAC rows on downed device as "removed" unconditionally.
- `apply_routing_reroute_adds()`: for single-path routes removed by nexthop dependency, finds the surviving OSPF neighbour in the same area and emits an `added` row with the new nexthop IP and interface. Key design: (1) verifies alternative has a surviving *unchanged* row in post-mutation rib_rows (not pre-failure snapshot) — correctly suppresses routes to the downed device's loopbacks; (2) derives nexthop IPs via subnet matching since `neighbor.ip` is None throughout; (3) cross-device VRF match uses `(device, network)` only — Juniper `"master"` ≡ Arista/Cisco `"default"`; (4) suppresses routes whose destination subnet is exclusively the downed device's (loopbacks).

**engine/boundaries.py**
- `vrrp-to-vrrp` same-level co-membership suppression added (alongside existing `stp-stp` and `vxlan-vxlan`). VRRP is terminal leaf — sharing gateway devices across VRRP groups does not cause cascade.

**engine/snapshot.py — iface_state + connectivity fetchers**
- `_fetch_iface_state()`: `tables/inventory/interfaces` → {device, interface, site_id, l1, l2}. Protocol-agnostic ground-truth for interface operational state.
- `_fetch_connectivity()`: `tables/interfaces/connectivity-matrix` → {device, interface, peer_device, peer_interface, protocol}. Deduplication by priority: cdp(0) > lldp(1) > stp(2) > cef(3). Bidirectional fill applied. Added to `fetch_snapshot()` as `"iface_state"` and `"connectivity"` keys.

**mutate_snapshot.py — interface_state.csv**
- `_build_peer_index()`: three-layer (device, interface) → (peer_device, peer_interface) lookup: connectivity-matrix (CDP/LLDP/CEF) → STP edges → subnet match on /30+/31 P2P links. Subnet match covers routed firewall-router links where neither device speaks CDP/LLDP.
- `apply_iface_state_changes()`: DEVICE_DOWN removes all downed-device iface_state rows + marks all discovered peers `changed, l1=down, l2=down`. INTERFACE_DOWN marks specific interface + its peer `changed, l1=down, l2=down`.
- New `interface_state.csv` output. `_write()` now reports `changed=` count.
- Results: scenario 2.2 changed=2 (d3xr01 Gi0/2 + d3xfw01 port2 via LLDP); scenario 1.1 removed=14 changed=11 (all physical + SVI peers of s1xgw1).

**zones/stp.py — identify_zones()**
- Now uses union-find on the STP edge graph to produce one zone per connected component, rather than one zone per `(site_id, vlan_id)`. Frankfurt VLANs 441–444 have zero inter-switch STP edges (bridged by VXLAN, not trunks), so each switch gets its own zone: `Frankfurt-4X:stp:vlan:441:s4xsw05`, `Frankfurt-4X:stp:vlan:441:s4xsw07`, etc. Single-component zones (all Paris VLANs) keep the plain id — backward-compatible.

**scripts/generate_zone_graph.py**
- VRRP zones included in HTML force-directed graph output (light pink nodes, `stp_to_vrrp` edge type, VRRP label transform). Day 4/5 graphs: 67 zones / 172 boundaries (was 63/172 after VRRP fix; was 56/123 before).

**Multi-start zones:** `engine/traversal.py` `_find_starting_zones()` filters hierarchical zones so only the upstream zone starts. All 4 Paris STP VLANs start simultaneously on s1xgw1 DEVICE_DOWN.

**Utility scripts**
- `run_scenario.py`: fetches live snapshot, runs propagation, writes `scenario_<device>_<event>[_<iface-slug>].json`. Accepts `--interface` for INTERFACE_DOWN events; interface stored in trigger JSON and included in output filename.
- `mutate_snapshot.py`: applies scenario JSON → CSVs with `mut` column (unchanged/removed/added). Tables: inventory, stp_ports, fhrp, mac, rib, interfaces. Branches on `event_type`:
  - `device_down`: existing full-sweep logic (inventory + FHRP/MAC + all interfaces removed)
  - `interface_down`: device stays up; only the specific interface row removed; `apply_routing_removals_interface_down()` removes routes via that nexthop_if on the device + routes on affected devices with nexthop_ip in the downed interface's IPs; reroute adds scoped to downed interface's IPs via `downed_ips` param.

**Live scenario 2.2 (d3xr01 Gi0/2 INTERFACE_DOWN, same snapshot) — post-30f4f95:**
- 13 zones traversed, stop_reason=terminal_boundary (broader than expected — engine marks full OSPF zone as affected, cascades to MPLS BGP and all site BGP zones; known model limitation)
- rib.csv: removed=191, added=187
  - d3xr01: 11 routes via Gi0/2 removed; 7 rerouted via d3xr02 (10.1.0.222/Gi0/3) for London subnets + reachable link subnets; 3 prefixes (d3xfw01-side link subnets) correctly unreachable — no alternative
  - d3xfw01: 91 routes via d3xr01 IP (10.1.0.98) removed; 91 rerouted via s3xgw1 (10.1.0.102/port3) — OSPF SPF via s3xgw1→s3xgw2→d3xfw02→d3xr02→d3xr01
  - split-horizon cleanup: 89 s3xgw1 routes via d3xfw01 (10.1.0.101) removed (loop: d3xfw01→s3xgw1, s3xgw1→d3xfw01 for same prefixes); 89 second-wave reroutes for s3xgw1 via s3xgw2
  - d3xr01's loopback (10.250.0.21/32) also gets rerouted via d3xr02 — sole-endpoint check now bypassed for INTERFACE_DOWN (device is alive)
- inventory/STP/FHRP/MAC: unchanged (device stays up) ✅

**Live scenario 1.1 (s1xgw1 DEVICE_DOWN, snapshot 5ef44b70-b4ea-4246-b85f-878ef5723b41):**
- 10 zones traversed, stop_reason=exit_empty
- 4 STP VLANs → 4 VRRP zones + OSPF area 0.0.0.0 → OSPF area 0.0.0.1
- VLAN 112 VRRP: no election (s1xgw2 already master — correct)
- VLAN 114: s1xgw1 was master at priority 85 (lower than s1xgw2's 90, preemption disabled)

**CSV outputs post-f0b4e19 (scenario_s1xgw1_device_down_mutated/):**

| CSV | Removed | Added |
|---|---|---|
| inventory.csv | 1 | 0 |
| stp_ports.csv | 24 | 0 |
| fhrp.csv | 7 | 3 |
| mac.csv | 30 | 42 |
| rib.csv | 216 | 78 |
| interfaces.csv | 14 | 0 |

rib.csv adds: 77 `vpn1xfw01` routes rerouted via s1xgw2 (`10.255.1.5 / GigabitEthernet0/2`); 1 `d1xfw01` route (`10.255.1.0/30`) rerouted via `d1xr01` (which is outside the propagation zone and has a surviving path). Known limitation: the wrong alternative is sometimes chosen when a device outside the propagation zone has a stale surviving row — cost-based SPF selection not implemented.

Known model limitation (accepted): `aac1.xxx` infrastructure MACs added to access switch uplink ports via STP TC re-learn walk are correct per the optimistic re-learn assumption — the walk assumes all traffic flows post-convergence.

---

**mutate_snapshot.py — ECMP stale-nexthop cleanup + SVI peer marking (post-f962ee6)**
- `apply_ecmp_stale_nexthop_cleanup()`: after all reroutes, removes ECMP rows where the nexthop device has no `unchanged` route for that prefix — only fires when at least one other ECMP nexthop survives. Scenario 2.2: edge3x01 loses 7 d3xr01 ECMP nexthops for London subnets (now single-path via d3xr02); s3xlbr01 loses 81 s3xgw1 ECMP nexthops (single-path via s3xgw2). Runs after all reroutes in both DEVICE_DOWN and INTERFACE_DOWN branches.
- `_is_physical_iface()`: returns False for SVIs (Vl*/Vlan*), loopbacks, tunnels, BVI/IRB, NVE. Used in two places:
  - `_build_peer_index()` subnet-match layer now restricted to physical interfaces — SVIs on the same IP subnet are a L3 IP relationship, not a P2P physical link; previously s1xgw1 Vl112 ↔ s1xgw2 Vl112 were being matched via subnet.
  - `apply_iface_state_changes()` only marks peers as `changed, l1=down, l2=down` when the downed interface is physical. SVIs don't have a real L1 signal. Scenario 1.1: interface_state changed=8 (was 11) — Vl112/113/114 on s1xgw2 no longer incorrectly marked down.

**mutate_snapshot.py — INTERFACE_DOWN reroute (post-30f4f95)**
- `reroute_downed_device=True` flag: triggering device's own removed routes (matched by `nexthop_if==downed_interface`) are now eligible for reroute adds. Sole-endpoint check bypassed when True (device is alive).
- `downed_interface` guard in candidate selection: skip OSPF neighbors reachable only via the downed interface. Area lookup uses interface-based match when `reroute_downed_device=True`.
- Loop-back candidate check (triggering device only): skip candidates whose surviving route for the prefix has nexthop_ip in the triggering device's own IPs — prevents d3xr01→edge3x01→d3xr01 triangle (edge3x01 was first in OSPF neighbor list and had ECMP including d3xr01).
- `apply_split_horizon_cleanup()`: after first-wave reroutes, for each add D→via-U-IP: finds U's unchanged routes for that prefix with nexthop=D's link IP, marks removed, returns {device, network, former_nexthop_ip, former_nexthop_device} for second-wave `apply_routing_reroute_adds()`.

## Known gaps / next work

1. **Device removal from all tables** — systematic pre-propagation pass for ARP, BGP/OSPF neighbors, LLDP, topology edges. Inventory + interfaces covered in mutate_snapshot.py; engine doesn't produce these as DeviceDiffs yet.

2. **Host MAC movement beyond VRRP virtual MAC** — regular host MACs that learned upstream via the downed device. STP `_compute_mac_diffs()` handles reconvergence (42 added rows), but relies on access-port anchors being present in snapshot MAC table.

3. **Routing reroute adds: alternative selection quality** — current algorithm takes the first OSPF neighbour with a surviving row that doesn't loop back, not lowest-cost. Devices outside the propagation zone may still be chosen if their route doesn't loop. Fix: weight by OSPF path cost, or restrict candidates to `affected_devices`.

4. **SVI state via last-L2-port check** — `_is_physical_iface()` now prevents SVIs from being marked via peer lookup. Next step: when a physical interface goes down, check if it was the LAST L2 port in its VLAN on the peer switch; if so, mark the peer's SVI for that VLAN as changed/down.

5. **ARP table** — not yet modelled. When an interface goes down, ARP entries whose gateway IP is on that interface should be removed from adjacent devices. Deferred explicitly.

6. **Vinny integration** — engine diffs at zone/table level; mutation backend needs prefix-level typed operations (`remove_route`, `add_route`, `remove_mac`, etc.). Bridge layer not yet built. Engine snapshot fetch already has routing table data; needs to be made available to `compute_diffs()` per zone module.
