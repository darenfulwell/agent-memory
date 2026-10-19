---
name: feedback-conceptual-page-development
description: For conceptual/architecture wiki pages, let Daren iteratively restate and refine the idea across several turns before writing a full draft
metadata:
  type: feedback
---

When revising or building a conceptual/architecture wiki page (as opposed to ingesting a new external source), Daren works through the idea incrementally across multiple turns — restating a concept, then correcting or adding a nuance, then adding another — rather than handing over a finished framing in one message. Each turn should get a tight synthesis back (what it maps onto in the existing page, at most one clarifying question) rather than a full section rewrite in response.

**Why:** Demonstrated across a 2026-09-16 session reworking [[network-segmentation-model]] (llm-wiki): the two-lineage framing (engineering blast-radius containment vs. security threat containment) arrived over four separate turns, each confirmed or redirected before the next — dropping the propagation-zone tier out to its own page, keeping "policy zone" (zone-based firewall, DMZ, VRF-to-VRF) distinct from microsegmentation rather than collapsing them, naming the DMZ as security's one purpose-built segment, then adding the IP-subnet/gateway refinement. Each landed as a small, targeted edit to the already-rewritten page, not a renewed full draft.

**How to apply:** In conceptual-page sessions, resist drafting prose after the first explanation — reflect the idea back, map it onto what already exists in the page/wiki, and ask at most one sharpening question. Once the user gives a clearly decisive structure (an explicit "draft it," or an instruction confident enough to act on), write the full page in one pass — the Write tool is fine for a near-total rewrite at that point. After that initial rewrite, treat further additions from the user as targeted Edits to specific sections, not further full rewrites. Related: [[feedback-wiki-writing-style]].
