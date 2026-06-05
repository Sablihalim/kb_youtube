# kb_youtube — LLM Wiki Schema & Workflow

This repository is a **personal, LLM-maintained knowledge base built from YouTube videos**, following the LLM Wiki pattern (Karpathy idea file). The LLM (you) builds and maintains the `wiki/` layer; the human curates sources, directs analysis, and asks questions.

> Sibling project: the user also runs the **Pureh** wiki (`Selsilah/Pureh`) on the same pattern. Keep conventions consistent with that mental model: `raw/` → `wiki/`, plus `index.md`, `log.md`, and this schema file.

**Domain:** mixed grab-bag, weighted toward **tech/AI** and **ideas/commentary** (talks, essays, podcasts, analysis).

---

## 0. Session startup (do this first, every session)

Before doing anything in this repo:
1. Read this file (`CLAUDE.md`).
2. Read `index.md` to see what pages exist.
3. Read the last ~5 entries of `log.md`:
   `Get-Content log.md | Select-String '^## \[' | Select-Object -Last 5`
   (bash: `grep "^## \[" log.md | tail -5`)

Then proceed with the user's request (ingest / query / lint).

---

## 1. Architecture

Three layers:

- **`raw/`** — immutable source material. Video transcripts, descriptions, your own notes. One file per video, named `<video-slug>.md`. Never edit files here after creation except to append the original captured text. `raw/assets/` holds thumbnails and screenshots.
- **`wiki/`** — everything you (the LLM) generate and own. Summaries, entity pages, concept pages, analyses. You create, update, cross-link, and keep these consistent.
- **Schema & bookkeeping** — this file plus `index.md` (catalog) and `log.md` (chronological record).

### Directory map

```
kb_youtube/
├── CLAUDE.md          # this file — schema + workflow
├── index.md           # catalog of every wiki page (content-oriented)
├── log.md             # append-only timeline (chronological)
├── README.md          # short human-facing intro
├── raw/               # immutable sources
│   ├── assets/        # thumbnails, screenshots
│   └── <video-slug>.md
└── wiki/
    ├── overview.md    # top-level entry point + evolving synthesis
    ├── sources/       # one summary page per video  (THE core unit)
    ├── channels/      # one page per YouTube channel
    ├── people/        # creators, hosts, guests, notable people discussed
    ├── concepts/      # ideas, frameworks, arguments, techniques (synthesized across videos)
    ├── references/    # external things videos point to: tools, papers, projects, books, links
    └── analyses/      # filed query results worth keeping
```

---

## 2. Naming & linking conventions

- **Filenames:** kebab-case, no dates in the name. `the-bitter-lesson.md`, `andrej-karpathy.md`, `lex-fridman-podcast.md`.
- **Video slugs:** short and human-readable, derived from the title, not the video ID. Store the real title + ID in frontmatter.
- **Links:** use Obsidian wikilinks `[[file-slug]]` or `[[file-slug|display text]]`. Link liberally — a link to a page that doesn't exist yet is a valid TODO marker, not an error.
- **Frontmatter:** every wiki page starts with YAML frontmatter (for Obsidian Dataview). Schemas below.
- **Dates:** absolute `YYYY-MM-DD`. Today is provided in session context — never write "today"/"yesterday".

---

## 3. Page types & frontmatter

### Source (video) — `wiki/sources/<video-slug>.md`
The core unit. One per video.
```yaml
---
type: source
title: "<full video title>"
channel: "[[channel-slug]]"
url: <youtube url>
video_id: <id>
published: <YYYY-MM-DD | unknown>
duration: <hh:mm | unknown>
ingested: <YYYY-MM-DD>
topics: [ai, agents, ...]
tags: [source]
---
```
Body sections: **TL;DR** (2–4 sentences) · **Key points** (bullets, the substance) · **Notable quotes** (optional, with rough timestamps if available) · **References mentioned** (links to `references/`) · **Connections** (how it relates to other wiki pages, with wikilinks) · **Raw source:** link back to the `raw/` file.

### Channel — `wiki/channels/<channel-slug>.md`
```yaml
---
type: channel
name: "<channel name>"
url: <channel url>
creator: "[[person-slug]]"
topics: [...]
tags: [channel]
---
```
Body: what the channel is about, recurring themes, list of ingested videos (wikilinks), take on its perspective/bias.

### Person — `wiki/people/<person-slug>.md`
```yaml
---
type: person
name: "<full name>"
roles: [creator, host, guest, referenced]
affiliations: [...]
tags: [person]
---
```
Body: who they are, their positions/views as they appear across videos, where they show up (wikilinks to sources).

### Concept — `wiki/concepts/<concept-slug>.md`
Ideas, frameworks, arguments, techniques — the synthesis layer.
```yaml
---
type: concept
name: "<concept name>"
domain: tech-ai | ideas | other
tags: [concept]
---
```
Body: clear explanation · how different sources treat it · **contradictions/debates** (flag explicitly when sources disagree) · related concepts (wikilinks) · sources that discuss it.

### Reference — `wiki/references/<ref-slug>.md`
External things videos point to.
```yaml
---
type: reference
name: "<name>"
kind: tool | paper | project | book | link
url: <url>
tags: [reference]
---
```
Body: one-paragraph what-it-is, why it came up, which sources mention it.

### Analysis — `wiki/analyses/<question-slug>.md`
A filed query result.
```yaml
---
type: analysis
title: "<the question>"
created: <YYYY-MM-DD>
tags: [analysis]
---
```
Body: the answer, synthesized with citations to wiki pages (wikilinks). End with **Sources** used.

---

## 4. Operations

### INGEST — add a new video
Trigger: user pastes a transcript, gives a URL, or drops a file in `raw/`.

1. **Acquire the source text.**
   - *Pasted text:* save it verbatim to `raw/<video-slug>.md` with a small header (title, url, captured date).
   - *URL given:* attempt `WebFetch` on the video URL to get title/description/transcript. If captions aren't retrievable, tell the user and ask them to paste the transcript or run a downloader (e.g. `yt-dlp --write-auto-sub --skip-download <url>`). Then save to `raw/`.
   - *File already in `raw/`:* read it.
2. **Read it fully.** Briefly discuss key takeaways with the user (1–3 sentences) unless told to batch silently.
3. **Write the source page** in `wiki/sources/` (schema above).
4. **Propagate** — create or update related pages:
   - `channels/` — add the video to the channel page (create channel page if new).
   - `people/` — creator + any notable people discussed.
   - `concepts/` — for each significant idea, create or enrich a concept page; flag contradictions with existing claims.
   - `references/` — tools/papers/projects/books the video mentions.
5. **Update `index.md`** — add the new source and any new entity pages to the catalog.
6. **Append to `log.md`** — `## [YYYY-MM-DD] ingest | <video title>` + one line on what was touched.

A single ingest typically touches 5–15 wiki pages. Reuse existing pages — search before creating to avoid duplicates.

### QUERY — answer a question against the wiki
1. Read `index.md` to locate relevant pages, then read them.
2. If the wiki is thin on the topic, say so; optionally offer a web search or to ingest more.
3. Synthesize an answer **with wikilink citations** to the pages used.
4. **Offer to file good answers** — if the answer is a reusable comparison/analysis/synthesis, propose saving it to `wiki/analyses/` (and updating `index.md` + `log.md`). Explorations should compound, not vanish into chat.

### LINT — health-check the wiki
Scan for and report (don't silently fix large changes — propose first):
- **Contradictions** between pages.
- **Stale claims** superseded by newer sources.
- **Orphan pages** with no inbound wikilinks.
- **Missing pages** — concepts/people/references mentioned a lot but lacking their own page.
- **Broken/missing cross-references** — pages that should link to each other but don't.
- **Data gaps** worth a web search or a new source.
Suggest concrete next questions to investigate and videos/channels to add. Log a `lint` entry.

---

## 5. index.md and log.md

- **`index.md`** is content-oriented: a catalog grouped by type (sources, channels, people, concepts, references, analyses), each line a wikilink + one-line summary. Update on every ingest and whenever pages are created.
- **`log.md`** is chronological and append-only. Every entry starts with `## [YYYY-MM-DD] <op> | <subject>` so it's greppable. Ops: `ingest`, `query`, `lint`, `note`.

---

## 6. Working style

- The human curates sources and asks questions; you do the reading, summarizing, cross-referencing, filing, and bookkeeping.
- Prefer enriching existing pages over creating near-duplicates. Search first.
- When sources disagree, **preserve the disagreement** — flag it on the concept page rather than smoothing it over.
- Keep summaries tight and skimmable. The value is in synthesis and connections, not transcription.
- This is a git repo of markdown — commit at natural checkpoints when asked.
