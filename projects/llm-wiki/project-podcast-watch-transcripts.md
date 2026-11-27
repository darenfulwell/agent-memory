---
name: project-podcast-watch-transcripts
description: How the podcast-watch scheduled task and on-demand transcript pulls fetch spoken.md content, and the constraints that shape that design
metadata:
  type: project
---

The weekly `podcast-watch` scheduled task (D:\users\daren\.claude\scheduled-tasks\podcast-watch\SKILL.md) confirms strong-match episodes using spoken.md's **free excerpt** page (`https://spoken.md/episode/<id>`), not the full paid transcript. `<id>` is the episode's Apple Podcasts `trackId`, resolved via the public, unauthenticated iTunes Lookup API (`itunes.apple.com/lookup?id=<show id>&entity=podcastEpisode`) — this replaced an earlier broken approach (`WebSearch site:spoken.md ...`) that failed because these episodes are too new (1-8 days old) for general search engines to have indexed yet, not because the content didn't exist.

Full (paid, credit-consuming) transcripts cannot be automated into the unattended weekly run, for two independent reasons: the scheduled-task sandbox blocks Bash network calls (curl) outright, and `WebFetch` (the only network tool available unattended) can't send custom headers, so `x-api-key` auth is impossible either way. spoken.md's own `/search` endpoint also 401s for an unauthenticated/unattended fetch.

**How to apply:** when Daren wants a full transcript for a specific episode (not the weekly default), pull it interactively via the Claude-in-Chrome browser tool: navigate to the episode page, click "Read the full transcript" (spends 1 of his spoken.md credits), then trigger a download via a Blob + temporary `<a download>` click rather than relaying text through tool output (DOM text-extraction tools truncate around 50-55k characters mid-transcript). Daren disabled Brave's "Ask where to save each file" setting specifically so this download lands in `Downloads/` without a Save-dialog prompt — don't suggest re-enabling it or re-explain the curl/WebFetch limitation; it's a settled design constraint, not an oversight. An interactive attempt to read his spoken.md API key out of `.env` and call curl directly was auto-denied by a permission hook guarding `.env` access — don't retry that path, it's a hook decision not a one-off fluke.

Once downloaded, the raw transcript is only source *material* — move it into `sources/podcasts/` as a plain raw file (not reformatted) until Daren actually listens/reads and asks for a normal ingest into a proper `.md` note per `sources/podcasts/README.md` format.
