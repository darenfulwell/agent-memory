---
name: feedback-wiki-writing-style
description: "When documenting structural/architectural relationships in the wiki, land the practical implication, not just the structural claim"
metadata:
  type: feedback
---

When writing up a layered or architectural model (e.g. how value/effort/risk propagates between components), don't stop at describing the structural relationship — explicitly state what it means for prioritization or decision-making.

**Why:** On llm-wiki's "ipf-product-layers" page, "Value Compounds Up the Stack" section (2026-10-19), the line that landed best with Daren was the practical-implication sentence — "prioritising Discovery improvements is not just foundational work with no visible payoff — every Discovery gain is already an Assurance gain and a Consumption gain, even before either of those layers ships a single new feature." The structural claim alone (Assurance/Consumption are functions of the layer below) is correct but inert; the payoff sentence is what makes it useful for a real decision (where to invest).

**How to apply:** After writing any section that describes a structural or causal relationship between parts of a model, add a closing sentence that translates it into "so this is what you should conclude/do/prioritize." Don't rely on the reader to derive the implication themselves.
