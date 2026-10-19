---
name: project-ipf-cloud-model
description: IP Fabric Cloud model release timeline and experiment deferral decision
metadata:
  type: project
---

Cloud zone experiment (AWS:890561831523/eu-central-1 added to Paris-1X DAG) is deferred pending IP Fabric 8.1.

**Why:** Waiting for pathlookup support across Cloud boundaries — needed to verify zone boundary predictions, not just model the structure.

**Timeline:**
- 7.12 — current lab version (no Cloud model)
- 8.0 — just released; first iteration of new Cloud model
- 8.1 — end of August 2026; second iteration with pathlookup support for Cloud

**How to apply:** When picking up Cloud zone experiment, use 8.1. The experiment is stubbed in `wiki/networks-and-systems/propagation-zone-experiments.md` (llm-wiki repo) as a placeholder. The AWS site to add is `AWS:890561831523/eu-central-1`.
