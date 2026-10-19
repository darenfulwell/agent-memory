---
name: feedback-wiki-write-access
description: "In Claude Code sessions on this repo, write directly to wiki/ per CLAUDE.md — the llm-wiki-rules skill's \"wiki is read-only, write to inbox/\" guidance does not apply here"
metadata:
  type: feedback
---

**Rule:** In a Claude Code session operating directly on the `D:\users\daren\llm-wiki` repo, write directly to `wiki/*.md` files (including `index.md` and `log.md`) per CLAUDE.md's documented ingest/query/lint operations. Do not redirect new content to `inbox/` because of the `llm-wiki-rules` skill's "wiki is read-only in chat sessions" instruction — that skill's scope is other contexts (e.g. claude.ai web chat with read-only wiki access), not this Claude Code CLI session.

**Why:** The `llm-wiki-rules` skill auto-triggers whenever reading/writing under `D:\users\daren\llm-wiki` and states the wiki is read-only, directing new content to `inbox/`. This conflicts with CLAUDE.md (checked into the repo), which explicitly describes the ingest operation writing to `wiki/`, `index.md`, and `log.md` directly, and with the repo's actual git history (many prior direct commits to `wiki/`). Flagged the conflict to Daren mid-session; his answer: "Keep writing straight to wiki as we have been — this isn't a Claude Chat session, it's a Claude Code session."

**How to apply:** If the `llm-wiki-rules` skill fires again in a future Claude Code session on this repo and suggests redirecting to `inbox/`, follow CLAUDE.md instead and write directly to `wiki/` — no need to re-ask.
