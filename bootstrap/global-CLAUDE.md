# Global instructions — all Claude Code sessions

## Personal memory: agent-memory

Daren maintains a portable, git-based personal memory repo (remote: https://github.com/darenfulwell/agent-memory, private). It is the canonical source for memory about Daren himself and about specific projects — see `CLAUDE.md` in that repo for the full schema (types, `global/` vs `projects/<slug>/`, file format).

The repo lives at a platform-dependent path. Try these in order until one resolves; if none do, ask Daren where it lives on this machine.

| Platform | Path |
|----------|------|
| Windows | `D:\users\daren\agent-memory` |
| macOS | `~/agent-memory` |
| Linux | `~/agent-memory` |

**At the start of any session, if relevant:** read `<agent-memory>/global/` for who Daren is and how he works, and check `<agent-memory>/MEMORY.md` for whether the current project already has a `projects/<slug>/` folder.

**Routing rule for new memory, in every project:**
- Never write full memory content into this machine's local per-project memory folder (Claude Code's own `.../projects/<project-slug>/memory/` path).
- Write the real file into `<agent-memory>/global/` (if it's true regardless of project) or `<agent-memory>/projects/<slug>/` (if scoped to the current project — create the folder if it doesn't exist yet), and add it to `<agent-memory>/MEMORY.md`.
- Leave a short pointer stub at the local project memory path (same filename, same frontmatter, body says "canonical copy has moved" + the agent-memory path) so it still surfaces in that project's own index, and add a one-line index entry there too.
- Do not use filesystem symlinks or hardlinks for this — they silently break on normal atomic-rename saves (confirmed on Windows; treat as true everywhere unless proven otherwise). The stub-and-pointer pattern is the durable approach.
- `user`-type memories always go to `global/`, never to a project folder.

**Promotion beyond this repo** (to a future team-facing `shared-memory` repo) is a separate, manual, formal step — never automatic — and only applies to `project`/`reference` types. Don't attempt it without Daren explicitly asking.
