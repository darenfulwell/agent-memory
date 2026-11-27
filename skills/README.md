# Skills

Canonical source for cross-project skills — the ones that should work the same way regardless of which project is active, as opposed to project-scoped skills (e.g. the llm-wiki's `/ingest-url`, `/lint-wiki`), which correctly live inside their own project repo instead.

## Deployment caveat

This folder is the **source of truth** you edit. It is not automatically read by Cowork or Claude Chat — those surfaces manage installed skills through their own plugin mechanism, in app-managed storage outside this repo. After editing a skill here, the deployed copy must be manually re-copied into place for the change to take effect.

Known deployed location for `memory` (as of 2026-11, may shift if Cowork reinstalls or propagates it to new session contexts):

```
D:\Users\daren\AppData\Roaming\Claude\local-agent-mode-sessions\skills-plugin\<session-uuid>\<session-uuid>\skills\memory\SKILL.md
```

**Why a copy instead of a link:** the same atomic-rename-breaks-links issue documented in the main `CLAUDE.md` applies here too — and separately, Cowork's own plugin system owns that path, so a future reinstall could replace it anyway. Treat `agent-memory/skills/` as the editable original and the deployed path as a build artifact.
