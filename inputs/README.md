# inputs/

Raw evidence — customer feedback, meeting notes, transcripts, competitor screenshots, secondary research. Inputs are dated and accumulate over time; you draw on them when authoring PRDs, doing competitive analysis, or making prioritization calls.

## Folder layout

```
inputs/
├── SCHEMA.md                       # canonical frontmatter values — read this first
└── YYYY-QN/                        # e.g., 2026-Q2/
    ├── customer-feedback/
    ├── meeting-notes/
    ├── transcripts/
    ├── competitor-analysis/
    └── secondary-research/
```

Inputs are partitioned by **quarter** (Jan–Mar=Q1, Apr–Jun=Q2, Jul–Sep=Q3, Oct–Dec=Q4) and **type**. The quarter is derived from each input's `date:` frontmatter field — you don't pick a folder manually.

## Filing new inputs

Run `/add-input` and paste/describe the raw content. The skill:

1. Detects the input type (customer feedback vs. meeting notes vs. transcript vs. competitor analysis vs. secondary research).
2. Asks any clarifying questions needed to fill the frontmatter (who, when, which features, sentiment, priority).
3. Writes the file to the correct `inputs/YYYY-QN/<type>/` folder with frontmatter conforming to `SCHEMA.md`.

Don't write input files by hand — the frontmatter schema is strict and `/import-inputs` relies on it for grep-based project relevance scanning.

## Frontmatter schema

Every input starts with YAML frontmatter. See **`SCHEMA.md`** for the canonical reference:

- `type` — one of `customer-feedback`, `meeting-notes`, `transcript`, `competitor-analysis`, `secondary-research`
- `date` — `YYYY-MM-DD`
- `features` — kebab-case slugs you define in `SCHEMA.md`
- `priority` — `critical | high | medium | low`
- `sentiment` — `positive | negative | neutral | mixed | urgent`
- `customers` — array of company names

**Define your feature slugs in `SCHEMA.md` before logging many inputs.** Once slugs are in use across many files, renaming them is expensive.

## Connecting inputs to projects

Project workspaces don't copy inputs — they reference them. After filing inputs, run `/import-inputs` from inside a project; it scans `inputs/` by frontmatter and appends relevant ones to the project's `input-references.md`.
