# Log — kb_youtube

Append-only chronological record. Each entry: `## [YYYY-MM-DD] <op> | <subject>`. Ops: `ingest`, `query`, `lint`, `note`.

Greppable: `grep "^## \[" log.md | tail -5` (PowerShell: `Get-Content log.md | Select-String '^## \[' | Select-Object -Last 5`).

---

## [2026-06-05] note | Wiki initialized
Scaffolded the kb_youtube LLM Wiki: schema (`CLAUDE.md`), `index.md`, `log.md`, `wiki/overview.md`, and the `raw/` + `wiki/` directory structure. Domain: mixed grab-bag, weighted to tech/AI and ideas/commentary. Ready for first ingest.
