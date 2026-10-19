---
name: project-agent-harness-design
description: "Design history for the agent-memory / shared-memory harness itself — decisions made before and during the build"
metadata:
  type: project
---

Daren wanted a custom, portable agent harness (git-repo based) to work more effectively with Claude across machines/projects, separate from the llm-wiki. The design below was agreed on 2026-10-17, then built starting 2026-10-19 — this file now lives inside the repo it describes.

Agreed three-layer shape:

1. **`agent-memory/` (this repo, personal)** — operational memory about Daren and his projects, typed-file pattern (user/feedback/project/reference, `MEMORY.md` index). Split into `global/` (travels everywhere — who Daren is, general working style) and `projects/<slug>/` (scoped — e.g. llm-wiki, ipfabric-product, agent-harness itself).
2. **`shared-memory/` (separate repo, not yet created, team-facing)** — same shape, but organized around **team projects**, not Daren's personal project slugs (deliberately decoupled namespaces — a tipping point will eventually force some memories to move from personal → shared, to be judged case by case). Only `project` and `reference` types are candidates for promotion; `user` type never promotes, `feedback` only promotes if reframed as a team convention rather than a personal preference. Promotion is manual and formal — never automatic, per `CLAUDE.md` in this repo.
3. **The llm-wiki stays as-is** — it's reference depth (rich, cross-linked topic pages), not an operational memory layer. It connects to this memory layer only via `reference`-type pointers (link to a wiki page, never copy its content in) to avoid the memory and wiki drifting out of sync independently. The wiki already has its own separate promotion channel to a shared surface — Confluence — which serves the same tacit→codified role for deep content that `shared-memory` will serve for operational memory.

**Why:** mirrors the tacit-vs-codified-knowledge conversation Daren has raised before — what's fine to keep personal/exploratory vs what's safe enough to be picked up by others. Also driven by IP Fabric confidentiality concerns: the llm-wiki already mixes in real product/customer context (`wiki/ipfabric-product/`), so nothing from the personal side should flow into a shared repo without an explicit, formal review/rewrite step.

**Build log:**
- 2026-10-19 — repo scaffolded at `D:\users\daren\agent-memory`, pushed to `https://github.com/darenfulwell/agent-memory` (private, personal GitHub). Seeded `global/user-profile.md` and `global/reference-communities.md`.
- 2026-10-19 — migrated llm-wiki and ipfabric-product scoped memories out of the Claude Code project memory at `d:\Users\daren\.claude\projects\D--users-daren-llm-wiki\memory\`, leaving pointer stubs there (filesystem symlinks/hardlinks don't survive atomic-rename saves on Windows, so the stub-and-pointer pattern is the durable approach, not a live link).
- `shared-memory` repo not yet started — revisit once a project/reference memory is actually ready to promote.

**How to apply:** when picking up further harness work, check this file's build log and the repo's current `MEMORY.md` for what's already migrated before assuming something is still "just a plan." See projects/llm-wiki/feedback-wiki-workflow.md and projects/llm-wiki/feedback-external-artifact-language.md for the existing analogous personal/shared split already operating informally between the wiki and Confluence.
