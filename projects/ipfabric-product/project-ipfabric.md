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
- **Application–Infrastructure Mapping (AIM)** — the dominant current workstream (shipping as part of 8.1). Three-layer model: Infrastructure Reality / Application Semantics / Enforcement & Intent. Ingests from Illumio (supported), Guardicore/Dynatrace/Datadog (open API), or manual CSV. Core reframe: AIM completes the SHOULD BE side of assurance at the terminal-intent tier (is my app secured / resilient / — second-order — is the right telemetry in place to know if it's performant), not just the infrastructure-config tier. See llm-wiki: application-infrastructure-mapping, network-assurance-architecture.

---

**Competitive intelligence:**

- **Forward Networks** — main direct competitor. HSA-based digital twin. Forward Predict GA confirmed 29 Sept 2026 — extends to control plane, predicts post-change forwarding state analytically. Forward AI runs on off-the-shelf Anthropic Sonnet/Haiku via AWS Bedrock (confirmed on-record, Tech Field Day) — not a custom-trained model despite 2024 marketing claiming "the world's leading networking LLM"; differentiation is their agentic/context-engineering layer over the digital twin, not the foundation model. See llm-wiki: forward-predict.
- **NetBrain** — reactive investigation, not continuous; high PS overhead at scale. AI Bot/GenAI Co-Pilot defaults to OpenAI (confirmed via NetBrain's own idea-exchange portal); separately model-agnostic via MCP (Claude/OpenAI/Copilot as front-ends) — same "works with whatever LLM you use" positioning IP Fabric uses for its own MCP server, so that's no longer a safe claim of advantage on its own. Application Path (their AIM-equivalent) pairs network context with *performance* data (APM/Dynatrace) where AIM leads with *enforcement* data (Illumio) — no confirmed NetBrain microsegmentation integration found. See llm-wiki: netbrain.
- **NetBox / Nautobot / Infrahub** — Source of Truth platforms; complementary, not competing (document intent rather than operational state).
- **Stablenet / Statseeker** — flagged to research; minimal detail currently.

---

**Ongoing themes in the wiki (as of October 2026):**

- **AIM is the dominant current workstream.** 8.1 MVP shipping with Illumio/CSV/API ingestion. AIM launch webinar "Map Apps To Critical Infrastructure" — Daren + Mike Aragon, Wed Oct 7 2026, Goldcast — narrative and brand-template deck both built (see llm-wiki: aim-webinar-narrative). Research threads in flight: bidirectional Datadog integration (pull direction committed, push/enrichment direction researched not committed), config-derived candidate application inference (LB/firewall/ACI/NSX-T, no third-party tool required), competitive positioning vs. NetBrain Application Path and Forward AI.
- Two framings added directly by Daren to application-infrastructure-mapping.md, flagged as sharpening the webinar: networks as "fossilised business intent" (change records capture the what, not the why; AIM reconstructs intent from dependency structure) and "AIM as the ontology layer for LLM-driven intent" (AIM is what stops an AI agent from guessing about your network — closes the semantic grounding gap that causes hallucination, the NAF community's documented production blocker for AI in NetOps).
- Propagation zone model and prediction capability — mature, ongoing refinement (control-plane convergence engine work, directed discovery, causation recording). See llm-wiki: propagation-zones, propagation-engine-architecture, continuous-discovery.
- Network assurance architecture reframed: AIM completes the SHOULD BE side at the terminal-intent tier, not just infrastructure-config. See llm-wiki: network-assurance-architecture.
