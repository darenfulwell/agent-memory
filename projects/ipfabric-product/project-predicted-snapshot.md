---
name: project-predicted-snapshot
description: "Vinny's snapshot modifier tool and its role in the propagation zone prediction pipeline"
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
