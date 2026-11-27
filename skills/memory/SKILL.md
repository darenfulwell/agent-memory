---
name: "memory"
description: "Load and maintain Daren's portable agent-memory repo — personal context, project state, and learned preferences that carry across sessions. Use this skill at the start of ANY new session (Chat or Cowork) to load context, even if the user hasn't explicitly asked — offer it proactively. Also triggers on: /memory, 'load my memory', 'what do you know about me', 'load context', 'remember', or any reference to the agent-memory repo. Handles both reading memory (session start) and writing new learnings back (mid-session corrections, post-work offers). If the user corrects your approach, states a preference, or makes a project decision worth preserving, this skill tells you how to capture it."
---

# Memory — Load & Maintain

Daren keeps a git-based personal memory repo (`agent-memory`) that holds operational context: who he is, how he works, what state each project is in, and feedback/corrections from past sessions. This skill connects to that repo, loads the right context for the current session, and writes new learnings back as they happen.

## Locating the repo

The repo lives at a platform-dependent path. Try these in order until one resolves:

| Platform | Path |
|----------|------|
| Windows | `D:\users\daren\agent-memory` |
| macOS | `~/agent-memory` |
| Linux | `~/agent-memory` |

If the folder isn't already connected to this session, request it using `request_cowork_directory` (Cowork) or note that it needs to be selected as the working folder. If none of the paths resolve, ask Daren where the repo lives on this machine.

## Loading context (session start)

Run this sequence at the start of every session, or when Daren says `/memory` or asks to load context:

### 1. Connect and read the index

Read `MEMORY.md` in the repo root. This is the one-line-per-file index — use it to understand what's available without reading everything.

### 2. Always load global context

Read every file under `global/`. These are facts about Daren himself — role, interests, working style, domain vocabulary, community references — that apply regardless of what project is active. There are currently only a few files here; read them all.

### 3. Match and load project context

Determine which `projects/<slug>/` folder is relevant:

- **If Daren has another folder connected** (e.g. `llm-wiki`, `ipfabric-product`, a code repo), check whether a matching project slug exists under `projects/`. Match by folder name, repo name, or obvious association.
- **If Daren says what he's working on** ("I'm working on the predicted snapshot", "let's do some wiki work"), match that to a project slug from the MEMORY.md index.
- **If nothing matches**, just load global context and move on — don't force a match or ask unless it would genuinely help.

When a project matches, read all files in that `projects/<slug>/` folder.

### 4. Confirm what was loaded

After loading, give a brief one-line confirmation: what global context is active, which project (if any) was matched, and how many files were read. Don't recite the contents — Daren wrote them and knows what's in there. Something like: "Loaded your profile and community refs, plus 4 files for ipfabric-product."

If nothing was matched to a project, mention that and offer: "No project match for this session — want me to load a specific one, or are we going freeform?"

## Writing learnings back (mid-session)

New memories get written as they happen, not queued for a session end that may never come.

### When to write immediately

- **Daren corrects your approach** — "don't do X, do Y instead" → capture as a `feedback` file in the appropriate project folder (or `global/` if it's not project-specific). Include the correction and why.
- **A project decision is made** — "we're going with approach X for the snapshot pipeline" → update the relevant `project` file, or create a new one if none exists.
- **A new reference surfaces** — "the API docs are at this URL", "Vinny's repo is here" → capture as a `reference` file.

Before writing, briefly confirm with Daren: "That sounds worth saving to agent-memory — OK to write it as a feedback entry under projects/llm-wiki?" One sentence, not a ceremony.

### When to proactively offer

After completing significant work — finishing a research task, building something, resolving a complex question — consider whether anything learned during the work is memory-worthy. If so, offer in one line: "Want me to save [specific thing] to agent-memory?"

Don't offer after trivial exchanges. The bar is: would a future session benefit from knowing this?

### How to write

Follow the schema in the repo's `CLAUDE.md` (which will be loaded as project instructions if agent-memory is the connected folder, or which you already read during the load step):

1. **Pick the right type** — `feedback` for corrections/preferences, `project` for goals/decisions/state, `reference` for pointers to external systems. `user` type is rare (only for changes to Daren's role/profile).

2. **Pick the right location** — `global/` only if true regardless of any project context. Otherwise `projects/<slug>/`. Create a new project folder if one doesn't exist yet for this project.

3. **Write the file** with frontmatter (`name`, `description`, `metadata.type`) and body. For feedback and project types, lead with the rule/fact, then `**Why:**` and `**How to apply:**`.

4. **Update `MEMORY.md`** — add one index line under the right group header. Keep it under ~150 characters: `- [Title](path/to/file.md) — one-line hook`.

5. **Don't duplicate the wiki.** If the content is deep reference knowledge (a topic, a framework, a person's background), it belongs in `llm-wiki`, not agent-memory. Add a `reference` entry that points to the wiki page instead.

### Updating existing memories

If a correction or decision supersedes something already in a memory file, update the existing file rather than creating a new one. Check the MEMORY.md index first to see if a relevant file already exists.

## Session-start behaviour (proactive offer)

When this skill is installed and a new session begins, Claude should notice the fresh session and offer to load memory context early in the conversation — before diving into whatever the user asked. A light touch: "Want me to load your agent-memory context for this session?" or, if the user's first message gives a clear project signal, just load it and confirm.

The goal is zero-friction: Daren shouldn't have to remember to say /memory every time. But if he's clearly just asking a quick factual question, don't gate the answer on loading memory first — answer the question and offer memory loading as a follow-up if the conversation continues.
