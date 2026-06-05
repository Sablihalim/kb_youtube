# Log — kb_youtube

Append-only chronological record. Each entry: `## [YYYY-MM-DD] <op> | <subject>`. Ops: `ingest`, `query`, `lint`, `note`.

Greppable: `grep "^## \[" log.md | tail -5` (PowerShell: `Get-Content log.md | Select-String '^## \[' | Select-Object -Last 5`).

---

## [2026-06-05] note | Wiki initialized
Scaffolded the kb_youtube LLM Wiki: schema (`CLAUDE.md`), `index.md`, `log.md`, `wiki/overview.md`, and the `raw/` + `wiki/` directory structure. Domain: mixed grab-bag, weighted to tech/AI and ideas/commentary. Ready for first ingest.

## [2026-06-05] ingest | Nvidia's Jensen Huang on the AI revolution… (CNA) — FIRST 33:00
First ingest. Source: YouTube `XVoyL8rzhWs` (CNA, interviewer Victoria Jen, 56:13 total). Captions pulled via yt-dlp (installed it; no transcript available via WebFetch), de-duplicated from the en-orig VTT, cut at 32:59. **Partial — final ~23 min not yet ingested.**
Pages touched: raw `jensen-huang-cna-interview.transcript.md` (+ original .vtt/.info.json); source [[jensen-huang-cna-interview]]; channel [[cna]]; people [[jensen-huang]], [[victoria-jen]]; concepts [[ai-five-layer-cake]], [[agentic-ai]], [[ai-and-jobs]], [[ai-us-china-competition]]; references [[nvidia]], [[tsmc]], [[chatgpt]], [[claude-code]]; updated [[index]] and [[overview]].

## [2026-06-05] ingest | Jensen Huang × CNA — FINAL ~23:00 (now full)
Completed the interview (33:00 → 56:13). Regenerated raw transcript to the **full** 00:00–56:13 and updated the source page scope from partial to full.
New pages: person [[morris-chang]]; concepts [[leadership-in-the-age-of-ai]], [[intelligence-as-a-commodity]].
Enriched: [[jensen-huang]] (upbringing, parents, work ethic, "die on the job", family); [[ai-and-jobs]] (150yr/2% GDP argument, tech-divide/abundance, sharpened "too lazy", the children test); [[nvidia]] (Grace Blackwell + Vera Rubin ~$1T, "smallest large company", retention); [[tsmc]] (Morris Chang, $1T to Taiwan). Updated [[index]] and [[overview]] (added Leadership theme; removed the "ingest final 23 min" open item).
Remaining gap: still one-sided on AI & jobs — needs an opposing-view source.

## [2026-06-05] ingest | Geoffrey Hinton × The Diary Of A CEO — AI & jobs COUNTERPOINT
Ingested to balance [[ai-and-jobs]]. Source: YouTube `giT0ytynSqg` (The Diary Of A CEO, host Steven Bartlett, 1:30:07, ~13M views, published 2025-06-16). Captions via yt-dlp (en-orig), de-duplicated; full transcript saved. Episode is mostly AI safety/existential risk; processed in depth for the jobs chapters (~39:51–54:29).
New pages: source [[hinton-diary-of-a-ceo]]; people [[geoffrey-hinton]], [[steven-bartlett]]; channel [[the-diary-of-a-ceo]]; concept [[ai-existential-risk]].
Rewrote [[ai-and-jobs]] as a two-sided debate (Huang optimist vs Hinton pessimist + points of agreement). Updated [[index]], [[overview]], and the Huang source page (gap closed).
Next suggested: an economist (e.g. Daron Acemoglu) to adjudicate with data — the crux is elasticity of demand by sector.

## [2026-06-05] lint | First health-check (2 sources, 27 pages)
Built the link graph. **Fixed:** 4 non-idiomatic `[[../../raw/…]]` path-style links in the two source pages → plain basename links (Obsidian resolves by note name; the old form was effectively broken and orphaned both raw transcripts). **Clean now:** no real broken links (only the illustrative `[[file-slug]]` examples in CLAUDE.md), no content orphans (only log.md, by design), no accidental contradictions (the Huang↔Hinton jobs tension is intentional and framed as a debate). **Suggested (not actioned):** create-on-demand pages for recurring entities (OpenAI, Ilya Sutskever, Xi Jinping, Trump, robotics/physical-AI, UBI); thicken [[ai-existential-risk]]; and the parked Acemoglu data-arbiter for [[ai-and-jobs]].
