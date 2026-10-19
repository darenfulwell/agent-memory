---
name: project-ipfabric
description: IP Fabric product context — what it is, key domain concepts, ongoing work themes
metadata:
  type: project
---

IP Fabric is a network intelligence / network digital twin platform. Core capability: discovers the complete network by following the forwarding plane (crawling from device routing/switching state rather than scanning known subnets), builds a behavioural model of actual network state (not intended/configured state), and enables assurance, compliance, and automation against that ground truth.

**Why:** The wiki contains several internal IP Fabric documents (competitive intelligence, positioning, architecture thinking) alongside external sources. Understanding the product context helps make ingest decisions and find genuinely relevant cross-links.

**How to apply:** When ingesting sources or answering questions touching IP Fabric, use this context to interpret terminology correctly and identify what's genuinely novel vs. confirming known things.

---

**Key product concepts in the wiki (llm-wiki repo):**

- **Propagation zones** — three-plane model (control plane zones, data plane / L3 reachability zones, policy zones). A zone is a set of network elements within which routing information propagates without transformation; boundaries separate zones and are either existence-based (binary) or information-flow (policy-shaped). See llm-wiki: propagation-zones.
- **Network digital twin** — forwarding-plane discovery, behavioral assurance (IS vs SHOULD BE), point-in-time snapshots. Three tiers: discovery, assurance, history. See llm-wiki: network-digital-twin.
- **Network assurance architecture** — codified vs tacit knowledge; Agent decision hub; Case Library as tacit knowledge store; five human governance touchpoints. See llm-wiki: network-assurance-architecture.
- **Network segmentation model** — computable segmentation: macro-segments, broadcast domains, micro-segments; blast radius, compliance scope, Zero Trust posture. See llm-wiki: network-segmentation-model.

---

**Competitive intelligence:**

- **Forward Networks** — main direct competitor. HSA-based digital twin. Forward Predict (launched May 2026, beta; GA fall 2026) extends to control plane — predicts post-change forwarding state analytically. Forward AI + open-source MCP server (Go). See llm-wiki: forward-predict.
- **NetBrain** — reactive investigation, not continuous; high PS overhead at scale.
- **NetBox / Nautobot / Infrahub** — Source of Truth platforms; complementary, not competing (document intent rather than operational state).
- **Stablenet / Statseeker** — flagged to research; minimal detail currently.

---

**Ongoing themes in the wiki (as of July 2026):**

- IP Fabric prediction capability experiment — scoping what it would take to build a control-plane convergence engine comparable to Forward Predict
- Propagation zone model refinement — boundary types, directional predicates, prediction algorithm
- Network assurance architecture — codified vs tacit knowledge; will be pushed to Confluence soon (update `synced_from` in the wiki's network-assurance-architecture page when that happens)
