---
name: feedback-wiki-workflow
description: Validated workflow patterns for operating this wiki — ingest rules, link style, scope limits
metadata:
  type: feedback
---

Always read `wiki/index.md` first before globbing the wiki. The index is the lookup; only open individual pages that the index suggests overlap with the source.

**Why:** The wiki will grow large; full glob is expensive and usually unnecessary. The index-first pattern was explicitly designed for this.

**How to apply:** During every ingest or query, read the index before any other wiki file.

---

Use `[[filename|Display Text]]` wikilink format always — never bare `[[Display Text]]` alone.

**Why:** Obsidian resolves wikilinks by filename, and all files use kebab-case. Bare display-text links break when the filename differs.

**How to apply:** Every internal link in every wiki page.

---

For internal documents sourced from Confluence, add `synced_from` and `last_synced` to the frontmatter. Use the Confluence tiny-link URL for `synced_from`. For local files not yet on Confluence, use the local path temporarily and update once the page is pushed.

**Why:** Living documents need a pointer back to the authoritative source and a record of when they were last synced.

**How to apply:** Any wiki page that was created from a Confluence page or internal document.

---

Confluence URLs in `inbox/` notes are fetch-and-ingest instructions — use `getConfluencePage` (Atlassian MCP) to retrieve the content, then run the full ingest workflow. If the MCP is not connected, flag as skipped and report to user rather than silently dropping.

**Why:** Workflow decouples the moment of uploading to Confluence from the moment of ingesting into the wiki.

---

The second-mention rule: don't create an entity page (person, concept, framework) on first mention — leave it inline. Promote to its own page only when a second distinct source brings it up.

**Why:** Keeps the wiki focused on ideas with real density, not stubs for every name dropped.

---

Keep a 2–5 page touch budget per ingest. If editing more than 5 pages for one source, question whether the edits are material or just thematic overlap.

**Why:** Prevents the wiki growing unmanageably as the source base scales.

---

Forward links only — when creating a new page, add wikilinks *from* it to existing pages. Don't open other pages just to add reciprocal backlinks. Obsidian's backlink pane handles that natively.

**Why:** Efficiency. Editing N pages to add backlinks costs N reads + N writes for zero additional user value.
