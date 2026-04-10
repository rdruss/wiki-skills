---
name: wiki-init
description: Use when bootstrapping a new personal wiki for any knowledge domain — research, codebase documentation, reading notes, competitive analysis, or any long-term knowledge accumulation project.
---

# Wiki Init

Bootstrap a new LLM-maintained wiki at a user-specified path.

## Pre-flight

Check whether an `AGENTS.md` already exists nearby. If yes, ask the user if they want to reinitialize or just continue with the existing wiki.

## Process

### 1. Gather configuration (one question at a time)

Ask:
1. **Where should the wiki live?** (absolute path, e.g. `~/wikis/ml-research`)
2. **What is the domain/purpose?** (one sentence)
3. **What types of sources will you add?** (papers, URLs, code files, transcripts, etc.)
4. **What categories should `index.md` use?**
   - Research default: `Sources | Entities | Concepts | Analyses`
   - Codebase default: `Modules | APIs | Decisions | Flows` — see `codebase.md` in this skill's directory for detailed codebase guidance
   - Or specify custom

### 2. Create directory structure

```
<wiki-root>/
├── AGENTS.md         ← wiki schema + conventions (how other skills find the wiki)
├── raw/              ← immutable source documents (you add these, LLM never modifies)
│   └── assets/       ← downloaded images, PDFs, attachments
├── wiki/
│   ├── index.md      ← content catalog: every page, one-line summary, by category
│   ├── log.md        ← append-only operation log
│   ├── sources/      ← source summary pages
│   ├── entities/     ← tools, frameworks, companies, people
│   ├── concepts/     ← ideas, patterns, methodologies
│   ├── comparisons/  ← side-by-side analyses
│   └── synthesis/    ← higher-level analyses combining multiple sources
```

Pages are filed by type into subdirectories. Filenames are lowercase, hyphen-separated slugs (e.g. `attention-is-all-you-need.md`).

### 3. Write `AGENTS.md`

```markdown
# Wiki Schema — <domain>

This is a personal knowledge base about <domain>, maintained by an LLM following the LLM Wiki pattern.

## Directory structure

- `raw/` — Immutable source documents. Never modify these.
  - `raw/assets/` — Downloaded images referenced by sources.
- `wiki/` — LLM-maintained markdown pages. The LLM owns this layer entirely.
  - `wiki/index.md` — Content catalog of all wiki pages.
  - `wiki/log.md` — Chronological activity log.

## Conventions

- Use `[[wikilinks]]` for cross-references between wiki pages.
- Add YAML frontmatter to every wiki page:
  ```yaml
  ---
  title: Page Title
  type: <source-summary | entity | concept | comparison | synthesis>
  created: YYYY-MM-DD
  updated: YYYY-MM-DD
  tags: []
  sources: []
  ---
  ```
- Page filenames: lowercase, hyphens for spaces (e.g. `agentic-coding.md`).
- Keep pages focused — one entity or concept per page.
- `sources` field references raw/ filenames or URLs of the original source material.

## Page types

- **source-summary** — Summary of a single ingested source. Filed in `wiki/sources/`.
- **entity** — A tool, framework, company, or person. Filed in `wiki/entities/`.
- **concept** — An idea, pattern, or methodology. Filed in `wiki/concepts/`.
- **comparison** — Side-by-side analysis of two or more things. Filed in `wiki/comparisons/`.
- **synthesis** — Higher-level analysis combining multiple sources. Filed in `wiki/synthesis/`.

## Workflows

### Ingest (one source at a time, interactive)

1. Read the source document in `raw/`.
2. Discuss key takeaways with the user.
3. Create a source-summary page in `wiki/sources/`.
4. Create or update relevant entity and concept pages.
5. Update `wiki/index.md` with new/changed pages.
6. Append an entry to `wiki/log.md`.

### Query

1. Read `wiki/index.md` to find relevant pages.
2. Read those pages and synthesize an answer.
3. If the answer is substantial, offer to file it as a new wiki page.

### Lint

1. Check for orphan pages (no inbound links).
2. Check for contradictions between pages.
3. Identify concepts mentioned but lacking their own page.
4. Suggest sources to investigate for gaps.

## Log format

Each log entry:
```
## [YYYY-MM-DD] <action> | <subject>
<brief description of what was done>
```

## Index Categories

<one per line, matching the user's chosen taxonomy>

## Conventions
- raw/ is immutable — skills never modify it
- log.md is append-only — never rewritten, only appended
- index.md is updated on every operation that adds or changes pages
- All pages live flat in wiki/pages/ — no subdirectories
- overview.md reflects the current synthesis across all sources
<if codebase domain>
- README boundary: wiki pages must not duplicate README content. Extract structural signals; link to the README for operational content (setup, contributing, running). When ingesting any README, also evaluate it for gaps and suggest edits.
</if>
```

### 4. Write `wiki/index.md`

```markdown
# Wiki Index — <domain>

<for each category>
## <Category Name>
<!-- entries added by wiki-ingest -->
```

### 5. Write `wiki/log.md`

```markdown
# Wiki Log

Append-only. Format: `## [YYYY-MM-DD] <operation> | <title>`
Recent entries: `grep "^## \[" log.md | tail -10`

---

## [<today>] init | <domain>
```

### 6. Optionally write `wiki/overview.md`

Ask the user if they want an evolving synthesis page. If yes:

```markdown
---
title: Overview
type: synthesis
created: <today>
updated: <today>
tags: [overview, synthesis]
sources: []
---

# <Domain> — Overview

> Evolving synthesis of everything in the wiki. Updated by wiki-ingest when sources shift the understanding.

## Current Understanding

*No sources ingested yet.*

## Open Questions

*Add questions here as they arise.*

## Key Entities / Concepts

*Populated as pages are created.*
```

### 7. Confirm

Tell the user:
- Wiki initialized at `<path>`
- Add sources to `raw/` manually, or run `wiki-ingest` directly with a URL or file path
- Run `wiki-lint` periodically to keep the wiki healthy
- `AGENTS.md` is how all other skills locate this wiki — do not move or delete it
