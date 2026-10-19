---
name: feedback-partner-enrichment-reasoning
description: How to judge whether enriching a partner platform's adjacent feature is competitive risk or stickiness — don't pattern-match on category overlap alone
metadata:
  type: feedback
---

**Rule:** When IP Fabric (or AIM) pushes computed context into a partner platform that has its own adjacent, shallower version of a similar-sounding feature (e.g. enriching Datadog's Network Path/NDM with zone and enforcement context), don't default to treating it as competitive risk just because the feature categories overlap on the surface. Check first whether the enrichment is a **live, continuously-served dependency** (only stays correct if the source keeps computing and re-pushing it) or a **one-time value transfer** the partner could retain independently. A continuous dependency is a stickiness mechanic, not a risk — it makes the case for running the source product visible to an audience who might never otherwise see it.

**Why:** I (Claude) flagged Datadog Network Path/NDM enrichment as a "coopetition" risk in the AIM↔Datadog research page; Daren pushed back directly ("I'm not convinced... surely the only way to enhance Datadog's network capability is with IP Fabric's insights... this feels like a good example of meeting people where their workflow already is"). On reflection the framing was simply wrong, not just debatable — it also directly contradicted a conclusion already established elsewhere in the wiki (`consumption-layer.md`'s "invisible infrastructure, meet practitioners where they are" thesis, illustrated by the CVS Health Hermes pattern). The correction generalises: category-label overlap ("both show network topology") is not the same as capability overlap, especially when the partner's version is architecturally incapable of reaching the source's depth (e.g. probe/poll-based vs. computed-model-based).

**How to apply:** Before flagging a partner-enrichment idea as competitive risk in any future positioning/research work, explicitly answer: (a) does the value require the source to keep running and re-serving it, or could the partner retain it statically? (b) is there an architectural reason the partner can't close the gap by scaling up what they already do? If (a) is "requires ongoing serving" and (b) is "yes, architecturally blocked," it's stickiness, not coopetition — say so plainly rather than hedging with "two honest framings, both true."

See llm-wiki: `aim-datadog-bidirectional-integration.md` § "Strategic consideration: stickiness through dependency, not coopetition risk" for the full corrected analysis and where this played out.
