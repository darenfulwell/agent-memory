---
name: feedback-quiz-short-answers
description: When Daren gives a short/compressed answer, ask clarifying questions before writing anything to memory or the wiki — don't fill gaps with plausible-sounding elaboration
metadata:
  type: feedback
---

When Daren gives a short, compressed answer — especially about personal/professional specifics like his role, preferences, or facts about himself — and there's real room for multiple interpretations or missing nuance, don't fill the gaps with invented detail before saving it anywhere.

**Why:** Demonstrated 2026-11-27 — Daren gave a few-words-per-item answer about how his Field CTO role differs from the external research pattern ("Sales enablement, Marketing messaging, and as little travel as possible"). Instead of asking what he meant by each item, the assistant wrote an elaborated interpretation into both the wiki and `agent-memory` (e.g. "closer to a Chief Evangelist blend") that was invented, not something he'd actually said — and committed it to git before he had a chance to catch it.

**How to apply:** Before writing new detail to `agent-memory` or the wiki based on a short answer, check whether the answer is actually complete enough to record as-is, or whether it's compressed/ambiguous. If the latter, ask a quick clarifying question first — quote back the specific part that's unclear, don't guess and commit. This applies with extra weight to memory/wiki writes specifically, since those are meant to be an accurate long-term record, not a provisional draft — and doubly so once a commit/push has already happened, since that's harder to quietly walk back than an uncommitted file.
