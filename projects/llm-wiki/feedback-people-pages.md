---
name: feedback-people-pages
description: Always enrich people pages with LinkedIn data via Brave browser — auto-search if no URL given
metadata:
  type: feedback
---

When creating a people page, always attempt LinkedIn enrichment via the Claude in Chrome tools (Brave browser, where the user is logged in to LinkedIn).

**Why:** The experience page gives rich career history not visible on the main profile. Confirmed working — use `/details/experience/` as the primary fetch; activity feed is often gated.

**How to apply:**
- User provides a LinkedIn URL → fetch directly
- No URL → search `https://www.linkedin.com/search/results/people/?keywords=<name>`, present top candidate(s) with name/title/location, ask user to confirm before fetching
- Fetch order: `<profile>/details/experience/` first (usually works), then `<profile>/recent-activity/all/` (often gated — try but don't block on it)
- If inaccessible, note it and proceed with what's known from other sources

When reading recent activity, **distinguish original posts from reshares**. LinkedIn activity feeds mix both. A reshare signals what someone finds worth amplifying but is not the same as their own words/views. Before attributing a viewpoint, check whether the person is the original author or sharing someone else's content. Note the distinction when summarising ("shared a post by X saying..." vs "posted that...").

**Why:** Attributed views should reflect what the person actually said, not what they amplified. Reshares can still indicate interests but shouldn't be quoted as original statements.
