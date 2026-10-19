---
name: feedback-external-artifact-language
description: "When building external artifacts (Confluence, whiteboards, slides), translate the wiki's zone/boundary vocabulary into standard networking language"
metadata:
  type: feedback
---

The "zone", "boundary", "existence-based", "information-flow", "propagation" vocabulary used throughout the wiki and design sessions is a personal mental model developed between Daren and Claude. Daren loves it for design-thinking purposes but it does not reflect the wider team's shared understanding of how networks hang together.

**Rule:** Keep the zone/boundary language in the wiki (it's the right tool for design). When assembling artifacts for external consumption (Confluence pages, whiteboards, Engineering presentations, Sales/SE enablement materials), translate to standard networking and industry terminology.

**Why:** Engineering, Product, and SA audiences will not have the zone/boundary frame as context. Starting with unfamiliar vocabulary creates a comprehension barrier before the ideas land.

**How to apply — rough translation table:**

| Wiki vocabulary | External artifact equivalent |
|---|---|
| Zone | Network segment / broadcast domain / VRF / routing domain (pick the specific one) |
| Existence-based boundary | Adjacency / reachability boundary / neighbour relationship |
| Information-flow boundary | Routing boundary / redistribution point / policy boundary |
| Zone DAG | Network topology / dependency map |
| Zone collapse | Failure / adjacency loss / routing table withdrawal |
| Blast radius | Impact scope / failure domain ("blast radius" is acceptable in DevOps/SRE contexts) |
| Boundary device | Gateway / border router / ABR / ASBR / edge node |
| Propagation | Failure propagation / impact spreading |
| Micro-segment | Security policy zone / DFW rule / firewall policy |

The underlying concepts are sound — the vocabulary just needs to meet the audience where they are.
