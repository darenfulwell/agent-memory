---
name: feedback-interface-state-architecture
description: Interface operational state is the protocol-agnostic ground truth for mutation modelling — don't proxy it through protocol tables
metadata:
  type: feedback
---

Interface operational state (l1/l2 up/down) is the correct abstraction layer for mutation modelling — not protocol-specific tables.

**Why:** stp_ports.csv is a valid L2 loop-prevention proxy for 802.1D/w/s networks but breaks for SPB (IEEE 802.1aq), FabricPath, TRILL, or IS-IS-based L2 fabrics. Similarly, deriving routing impact from per-protocol adjacency tables requires separate inference for each protocol. Interface state is the common foundation: it's true regardless of what L2 or L3 protocol runs on top.

**How to apply:** The mutation model should have interface_state.csv as a first-class output table (from tables/inventory/interfaces — l1/l2 state). When an interface goes down, mark it changed there first. Protocol-specific effects (OSPF adjacency loss, STP reconvergence, connected route removal) are consequences, not the primary signal. For DEVICE_DOWN, peer interfaces on neighboring devices go to l1=down; for INTERFACE_DOWN, both sides of the link go to l1=down. ARP entries on the downed interface are a follow-on consequence (separate arp.csv table). Architecture: interface_state → rib + arp → protocol-specific zone diffs.
