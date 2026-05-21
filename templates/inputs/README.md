# templates/inputs/

Scaffolds for raw input files. `/add-input` picks one based on detected input type.

## Files alongside this README

| File | Input type | Purpose |
|---|---|---|
| `customer-feedback.md` | `customer-feedback` | Pain point + verbatim quote + customer attribution. |
| `meeting-notes.md` | `meeting-notes` | Attendees, agenda, decisions, action items. |
| `competitor-analysis.md` | `competitor-analysis` | Competitor capability snapshot or feature teardown. |

## Input types without a template file

`/add-input` handles two input types **inline** rather than from a template file:

- **`transcript`** — Verbatim call/interview transcripts (often pasted in bulk; the skill generates structure on the fly).
- **`secondary-research`** — Analyst reports, articles, surveys (heterogeneous enough that a fixed template would constrain more than help).

If you find yourself wanting a canonical structure for either, add a template file here and update `/add-input` to use it.

## Frontmatter

All templates use the canonical schema defined in `inputs/SCHEMA.md`. If you change a template's frontmatter shape (adding a field, renaming a slug, changing an enum value), update `SCHEMA.md` at the same time — `/add-input` and `/import-inputs` both rely on it.
