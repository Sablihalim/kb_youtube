# kb_youtube

A personal, LLM-maintained knowledge base built from YouTube videos, using the [LLM Wiki pattern](https://en.wikipedia.org/wiki/Memex) (Karpathy idea file). Domain: mixed, weighted to **tech/AI** and **ideas/commentary**.

The LLM builds and maintains the `wiki/`; I curate sources and ask questions. Best used with an LLM agent (Claude Code) on one side and Obsidian open on the other.

## Layout
- `raw/` — immutable source text (video transcripts, descriptions, notes). `raw/assets/` for thumbnails/screenshots.
- `wiki/` — LLM-generated pages: `sources/`, `channels/`, `people/`, `concepts/`, `references/`, `analyses/`, plus `overview.md`.
- `index.md` — catalog of all pages · `log.md` — chronological record · `CLAUDE.md` — schema & workflow for the LLM.

## Operations
- **Ingest** — "ingest this video" + paste a transcript, give a URL, or drop a file in `raw/`. The LLM summarizes it and updates entity/concept pages.
- **Query** — ask a question; the LLM answers from the wiki with citations and can file the answer to `analyses/`.
- **Lint** — "lint the wiki"; the LLM health-checks for contradictions, orphans, stale claims, and gaps.

## Getting a transcript
Paste it, give a URL (the LLM will try to fetch), or generate one with e.g.:
```
yt-dlp --write-auto-sub --sub-lang en --skip-download "<youtube-url>"
```
then drop the resulting subtitle file into `raw/`.
