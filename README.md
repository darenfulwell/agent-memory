# agent-memory

Daren's portable, git-based personal memory and cross-project skills — the piece that lets Claude pick up who he is, how he works, and what state a project is in, regardless of which machine or which surface (Claude Code, Cowork, Claude Chat) he's using.

## What's here

| Path | Purpose |
|---|---|
| `CLAUDE.md` | The schema: memory types, `global/` vs `projects/<slug>/`, file format, promotion rules |
| `MEMORY.md` | Index of every memory file, one line each |
| `global/` | Facts about Daren that apply regardless of project |
| `projects/<slug>/` | Facts scoped to one project or repo |
| `skills/` | Canonical source for cross-project skills (currently: `memory`) — see `skills/README.md` for the deployment caveat |
| `bootstrap/` | Files to copy into place when setting up a new machine (this doc tells you when) |

## Setting up on a new machine

1. **Clone this repo** to the expected path — the `memory` skill and the Claude Code global config both search these in order:

   | Platform | Path |
   |---|---|
   | Windows | `D:\users\daren\agent-memory` |
   | macOS | `~/agent-memory` |
   | Linux | `~/agent-memory` |

2. **Claude Code**: copy `bootstrap/global-CLAUDE.md` to the user-level Claude Code config path (`~/.claude/CLAUDE.md`, i.e. `d:\Users\daren\.claude\CLAUDE.md` on Windows). This is what makes every Claude Code session on the new machine — in any project — aware of `agent-memory` and the routing rule, without having to repeat this conversation per project. If that file already has unrelated content on the new machine, merge rather than overwrite.

3. **Cowork / Claude Chat**: these don't read local Claude Code config, so they need the `memory` skill (source: `skills/memory/SKILL.md`) installed through Cowork's own skill mechanism instead. This repo doesn't auto-deploy it — see `skills/README.md` for why (Cowork manages installed skills from its own app-managed storage, not from git) and where the currently-known deployed copy lives.

4. **Confirm it's wired up**: start a fresh Claude Code session in any project and ask whether it has `agent-memory` context loaded, or just watch whether it behaves like it knows who Daren is without being told.

## Design history

See `projects/agent-harness/project-agent-harness-design.md` for the full reasoning behind the shape of this repo — the three-layer split (personal `agent-memory` here / a team-facing `shared-memory` repo, not yet built / the `llm-wiki` repo as linked reference depth that never gets duplicated in here) and the build log of how it actually came together.
