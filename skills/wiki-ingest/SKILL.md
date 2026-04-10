---
name: wiki-ingest
description: Use when adding a new source to a wiki — a paper, article, URL, file, transcript, or any document. One ingest may touch 10-15 wiki pages.
---

# Wiki Ingest

Add a source to the wiki. Read it, discuss with the user, write a summary page, update entity/concept pages, and maintain the index and log.

## Pre-condition

Search for `AGENTS.md` starting from the current directory and upward, or in common wiki locations (`~/wikis/`). If not found, tell the user to run `wiki-init` first.

Read `AGENTS.md` to learn: wiki root path, page types and directories, frontmatter format, cross-reference convention, log entry format, index category taxonomy.

## Process

### 1. Accept the source

The source can be:
- **File path** — read it directly; copy to `raw/<filename>` if not already there
- **URL** — use the `browse` skill to fetch it; save to `raw/<slug>.<ext>`
- **Pasted text** — use what was provided

### 2. Read the source in full

Read all content. For long sources, read in sections. Do not skip.

### 3. Surface takeaways — BEFORE writing anything

Tell the user:
- 3-5 bullet points of key takeaways
- What entities/concepts this introduces or updates
- Whether it contradicts anything already in the wiki (read `wiki/index.md` and relevant pages to check)

Ask: **"Anything specific you want me to emphasize or de-emphasize?"**

Wait for the user's response before proceeding.

### 4. Generate the slug

Lowercase, hyphens, no special characters.
Example: "Attention Is All You Need" → `attention-is-all-you-need`

### 5. Write the source summary page

Write `wiki/sources/<slug>.md`:

```markdown
---
title: <source title>
type: source-summary
created: <today>
updated: <today>
tags: [<relevant tags>]
sources: [raw/<filename>]
---

# <Source Title>

**Source:** <original URL or file path>
**Date ingested:** <today>
**Type:** <paper | article | transcript | code | other>

## Summary

<2-3 paragraph synthesis — your own words, not abstract copy-paste>

## Key Takeaways

- <bullet>

## Entities & Concepts

<list of entities/concepts as [[slug]] links>

## Relation to Other Wiki Pages

<how this connects to or updates existing knowledge>
```

### 6. Update entity and concept pages

For each entity/concept touched by this source:

- **Page exists:** Read it, add to or update the relevant section, add the raw source to frontmatter `sources` list, update `updated` date
- **Page doesn't exist:** Create it in the appropriate directory:

For entities — write `wiki/entities/<slug>.md`:

```markdown
---
title: <Entity Name>
type: entity
created: <today>
updated: <today>
tags: [<relevant tags>]
sources: [raw/<filename>]
---

# <Name>

## Description

<synthesis across all sources that discuss this>

## Appearances in Sources

- [[source-slug]] — <one-line note>

## Related Concepts

- [[related-slug]] — <relationship>
```

For concepts — write `wiki/concepts/<slug>.md`:

```markdown
---
title: <Concept Name>
type: concept
created: <today>
updated: <today>
tags: [<relevant tags>]
sources: [raw/<filename>]
---

# <Name>

## Description

<synthesis across all sources that discuss this>

## Appearances in Sources

- [[source-slug]] — <one-line note>

## Related Concepts

- [[related-slug]] — <relationship>
```

### 7. Backlink audit — do not skip

Scan ALL existing pages across `wiki/sources/`, `wiki/entities/`, `wiki/concepts/`, `wiki/comparisons/`, and `wiki/synthesis/` for any that mention this source's entities/concepts but don't yet link to the new page. Add `[[new-slug]]` references where appropriate.

This is the step most commonly skipped. A compounding wiki's value comes from bidirectional links.

### 8. Update `wiki/index.md`

Add an entry under the correct category:
```
- [[<slug>]] — <one-line summary> _(ingested <date>)_
```

For any new entity/concept pages created, add those too under their respective categories.

### 9. Update `wiki/overview.md` (if it exists)

Re-read the current overview. If this source:
- Introduces a significant concept: add it to "Key Entities / Concepts"
- Shifts the overall understanding: update "Current Understanding"
- Raises a new question: add it to "Open Questions"

Update the frontmatter `updated` date.

If `overview.md` does not exist, skip this step.

### 10. Append to `wiki/log.md`

```
## [<today>] ingest | <source title>
Pages written: <slug>
Pages updated: <comma-separated list>
```

### 11. Report to user

- Summary page: `wiki/sources/<slug>.md`
- Entity/concept pages created or updated: <list with directories>
- Pages that received backlinks: <list>
- Index updated
