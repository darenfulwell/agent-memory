---
name: feedback-wiki-context-workflow
description: How and when to use the wiki-context skill to seed coding and requirement-gathering sessions with wiki knowledge
metadata:
  type: feedback
---

Run `/wiki-context <topic>` at the start of any coding experiment, requirement-gathering session, or planning task where the wiki is likely to have relevant domain knowledge.

**Why:** The wiki accumulates precise terminology, frameworks, and mental models (e.g. propagation zones, IS vs SHOULD BE, blast radius derivation). Without explicitly loading this context, future Claude sessions start from generic knowledge and drift toward imprecise language or miss the architectural framing already established in the wiki. Daren explicitly built this skill to avoid that drift.

**How to apply:**
- When a new coding or design session begins and the topic overlaps with wiki content (networks, infrastructure, IP Fabric product), invoke `/wiki-context <topic>` before gathering requirements
- The skill reads the wiki index, selects 3–5 relevant pages, and outputs: (1) a structured context block with Key Concepts / Terminology / Frameworks / Tensions / Source Pages, and (2) a Requirements to Explore section with themed questions derived from the wiki's frameworks
- The Requirements to Explore section is intentionally positioned after the context block — it surfaces what the wiki's frameworks reveal as load-bearing decisions before coding begins
- Close of the requirements section invites the user to pick the murkiest area: "Which of these is the murkiest right now?"

**Skill location:** `.claude/skills/wiki-context/SKILL.md` + `.claude/commands/wiki-context.md` (both needed — skill enables auto-triggering, command enables the `/wiki-context` slash command)
