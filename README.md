# kb_youtube

A personal, **LLM-maintained knowledge base built from YouTube videos** (the "LLM Wiki" pattern — Karpathy idea file). Instead of re-deriving answers from raw transcripts on every query, the LLM compiles each video into a persistent, interlinked wiki that compounds over time. Domain: mixed, weighted to **tech/AI** and **ideas/commentary**.

The LLM (Claude Code) builds and maintains the `wiki/`; you curate sources and ask questions. Best used with Claude Code on one side and the vault open in **Obsidian** on the other (graph view shows the shape of what you know).

## Layout
- `raw/` — immutable source material: cleaned video transcripts + the original `.vtt` captions and `.info.json` metadata. `raw/assets/` for thumbnails/screenshots.
- `wiki/` — LLM-generated pages: `sources/` (one per video), `channels/`, `people/`, `concepts/` (the synthesis layer), `references/` (tools/papers/projects/companies), `analyses/` (filed query results), plus `overview.md`.
- `index.md` — catalog of every page · `log.md` — chronological record · `CLAUDE.md` — the schema & workflow the LLM follows.

## Operations
- **Ingest** — "ingest this video" + a YouTube URL, a pasted transcript, or a file dropped in `raw/`. The LLM writes the source page and propagates to entity/concept pages, then updates `index.md` + `log.md`.
- **Query** — ask a question; the LLM answers from the wiki with `[[wikilink]]` citations and can file the answer into `analyses/` so explorations compound.
- **Lint** — "lint the wiki"; the LLM health-checks for broken links, orphans, contradictions, stale claims, and coverage gaps.

## Getting a transcript
WebFetch **cannot** pull YouTube captions (it only sees the page title), so transcripts come from `yt-dlp` (installed in the local Python). The LLM runs, for each video:
```
python -m yt_dlp --write-auto-sub --sub-langs en-orig --sub-format vtt \
  --skip-download --write-info-json -o "raw/<slug>.%(ext)s" "<youtube-url>"
```
YouTube auto-caption VTT has rolling duplicate lines and inline `<c>` word-timing tags; the LLM cleans these (keep plain lines, collapse consecutive duplicates) into `raw/<slug>.transcript.md`. The `.info.json` provides chapters + metadata. You can also just paste a transcript or drop your own file in `raw/`.

## Current contents
- **Sources (2):**
  - *Jensen Huang × CNA* — the AI revolution, jobs, China, Taiwan, leadership (full interview).
  - *Geoffrey Hinton × The Diary Of A CEO* — AI risk and the **jobs counterpoint**.
- **Highlight:** `wiki/concepts/ai-and-jobs.md` holds the two poles as a side-by-side debate (Huang optimist vs. Hinton pessimist), including where they actually agree.
- See `index.md` for the full catalog and `overview.md` for themes + open questions.

## Notes
- It's just a git repo of Markdown — version history, diffs, and branching come free.
- Obsidian plugins that pair well: Graph view, Dataview (pages carry YAML frontmatter), Marp (slides).
