# Agent Memory — Schema

Portable, git-based personal memory for working with Claude (or any compatible agent) across machines and projects. This repo is the **operational memory layer**: who Daren is, how he likes to work, and what state each project is in. It is not a knowledge base — for deep reference content see the `llm-wiki` repo, which this memory links to but never duplicates.

## Structure

```
agent-memory/
├── CLAUDE.md              → this file
├── MEMORY.md              → index of every memory file, one line each
├── global/                → true regardless of project or repo
│   └── <type>-<slug>.md
└── projects/
    └── <project-slug>/    → scoped to one project/repo
        └── <type>-<slug>.md
```

- **`global/`** — facts about Daren himself, general working-style preferences, and references that apply everywhere (communities, people, tools he always uses). Load these at the start of any session regardless of which project is active.
- **`projects/<slug>/`** — facts scoped to a single project or repo (e.g. `llm-wiki`, `ipfabric-product`). Only load the folder matching the current working directory.

## Memory types

Same four types in every folder, distinguished by filename prefix and `metadata.type` in frontmatter:

| Type | Contains | Example |
|------|----------|---------|
| `user` | Role, responsibilities, domain knowledge, background | `global/user-profile.md` |
| `feedback` | Corrections and confirmations about how to approach work — include *why* | `projects/llm-wiki/feedback-wiki-workflow.md` |
| `project` | Ongoing goals, decisions, state — include **Why:** and **How to apply:** | `projects/ipfabric-product/project-cloud-model.md` |
| `reference` | Pointers to where information lives in external systems | `global/reference-communities.md` |

### File format

```markdown
---
name: kebab-case-slug
description: one-line summary, used to judge relevance
metadata:
  type: user | feedback | project | reference
---

Body. For feedback/project: lead with the rule or fact, then **Why:** and **How to apply:**.
Link related memories with [[other-slug]] (resolved by filename within this repo).
Link to wiki pages by relative path or topic name, e.g. "see llm-wiki/wiki/ai/mcp.md" —
never copy wiki content into a memory file; memory should point, not duplicate.
```

### `MEMORY.md`

One line per file, under ~150 characters: `- [Title](path/to/file.md) — one-line hook`. Grouped by `global` / `projects/<slug>`. This is an index, not content — never write memory body text directly into it.

## Rules

- **Don't duplicate the wiki.** If something is deep reference knowledge (a topic, a framework, a person's background), it belongs in `llm-wiki`, not here — add a `reference` entry that points to it instead.
- **Global vs project is a judgment call, not a default.** Only file something under `global/` if it would still be true in a context that has nothing to do with the current project (e.g. Daren's role, his general communication preferences). Anything tied to one codebase, product, or initiative goes under `projects/<slug>/`.
- **New project folders are created on demand** — when memory-worthy context first appears for a project that doesn't have a folder yet.
- **Promotion to the shared-memory repo is manual and formal, never automatic.** A `project` or `reference` entry here may eventually be copied into the separate `shared-memory` repo (organized by team project) once it's reviewed and rewritten to remove anything personal or confidential — this never happens as a side effect of writing here. `user` memories never promote. `feedback` only promotes if reframed as a team convention rather than a personal preference.
- Keep the same exclusions as any good memory system: don't store what's derivable from code, git history, or docs already checked in elsewhere.
