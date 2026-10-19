---
name: feedback-external-confidentiality
description: Confidentiality discipline for any artifact meant to leave the wiki — strip customer names, internal ticket/roadmap refs, and NDA-only source material
metadata:
  type: feedback
---

**Rule:** When producing anything meant for an external or broader-than-PM audience (webinar slides, a standalone shareable doc, Confluence pages visible beyond the design team), strip: named customer accounts, Gong call quotes, internal Jira/epic ticket numbers, internal-only roadmap detail (quarter labels explicitly marked "not committed dates"), and competitor intel sourced from a sales call. Replace customer specifics with anonymised framing (e.g. "enterprise customers running microsegmentation at scale"). Add an explicit confidentiality guardrail callout at the top of any wiki page whose content might later feed an external artifact.

**Why:** Several internal Confluence source docs used while building AIM wiki pages were explicitly marked NDA-only, and an SA-enablement deck used as a structural template (`AIM-MVP-Enablement-8.1.pptx`) contained named customers (MMC, Bet365) and a slide literally titled "ROADMAP (INTERNAL)" with Jira epic numbers (IPF-1814/1923/2030/2031). Building a public AIM launch webinar deck from that material required deliberate stripping, not light editing — the gap between "internal reference" and "safe to present" is large and easy to under-estimate.

**How to apply:** Before finalising any artifact that will be presented, recorded, or shared outside IP Fabric's internal PM/sales org, re-read it specifically hunting for named accounts, ticket IDs, and internal-only roadmap commitments. When building a standalone external document from wiki content, also strip internal wikilinks and inline-explain any concept the wiki only linked to rather than defined.

See [[feedback-external-artifact-language]] — the sibling rule for vocabulary (zone/boundary → standard networking terms). This rule is about *what* content to remove; that one is about *how* to phrase what remains.
