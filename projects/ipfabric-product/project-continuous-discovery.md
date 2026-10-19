---
name: project-continuous-discovery
description: "Continuous discovery initiative — why/architecture, competitive positioning, and the live Confluence discussion draft"
metadata:
  type: project
---

Continuous discovery is an active IP Fabric product initiative: replace periodic, full-network discovery-then-snapshot with always-on, prioritised, diff-gated collection, plus periodic immutable "checkpoints" for comparison and audit.

**Why:** Full-network discovery is too slow to sit either side of a change window — exactly when customers need before/after comparison most. The September 2026 "Path Lookup in Three Tenses" hackathon (Adam Brezovjak & Michael Waligora) validated appetite for "current" data with a narrow live-view pathlookup; continuous discovery is the generalisation that doesn't require a user to already know what to ask about.

**Key design points (fully developed in llm-wiki):**
- Two layers: continuous diff-gated collection (no change → no write/trigger) + incremental, zone-bounded recomputation (bounded to device/device-pair, not full-network recompute)
- Scheduling has a cadence floor (volatility-weighted, edge/ephemeral faster than core) plus a trigger-based accelerant (config change / link-down jumps the queue), reusing the prediction engine's existing trigger-classification logic
- The propagation engine (zone graph traversal) is now formalised as a shared component with three consumers: directed discovery (real), mutation (synthetic/predicted), causation recording (comparison) — see llm-wiki: propagation-engine-architecture
- Refinement from session discussion (2026-10-05): directed discovery should deliberately overshoot the propagation engine's predicted stop condition, not just confirm within it — otherwise the calibration loop can only detect over-prediction, never under-prediction
- "Checkpoint" is the proposed sharper term for what a snapshot becomes once discovery is continuous: not the output of a slow crawl, but a deliberate materialisation of already-current state. Whether it replaces "snapshot" in customer-facing language is still open.

**Competitive positioning developed 2026-10-05:**
- NetBrain *does* claim "digital twin" positioning — the wedge is not that they avoid the term, it's that their snapshot/historical capture is limited in scope and slow in practice (correction applied after an initial draft got this wrong — see llm-wiki: netbrain.md)
- Forward Networks shares IP Fabric's current point-in-time-collection limitation; near-real-time freshness is a second, independent competitive front against them, distinct from the existing coverage/confidence argument (broad+confidence-rated vs. Forward's narrower OSPF/BGP/IS-IS-only certainty claim)
- AIM (Application Infrastructure Mapping) layered on a continuously current twin is the dimension neither competitor can match — maintaining network intent in application terms (secure/resilient/performant), closing the "network is working" vs "application is working" gap continuously rather than reactively

**Live artefact:** Confluence draft page created and iterated in the PM space, under folder `6062145623` — "Continuous Discovery — Why 'Live View' Isn't Enough (Discussion Paper)", page ID `6519455794`, tiny URL `/x/MgCXhAE`. Status is still `draft` as of 2026-10-05 — Daren has not yet asked for it to be published. References both RFC0036 (ENG space, Tomas Smida) and the June 2026 Discovery/Prediction/AIM discussion paper.

**Connects to:** "Project Thunder" — referenced in llm-wiki (path-lookup-three-tenses.md) as the umbrella initiative this hackathon partially previews (change propagation prediction + directed discovery, generalised across zone types with explicit confidence tiers). Worth checking for a dedicated Project Thunder memory/reference next time it comes up in detail.

**How to apply:** When continuous discovery comes up again, check the Confluence draft's current status (draft vs. published) before assuming its state, and check llm-wiki's `wiki/ipfabric-product/continuous-discovery.md` + `propagation-engine-architecture.md` for the fullest technical detail — this memory file is a pointer/summary, not the source of truth.
