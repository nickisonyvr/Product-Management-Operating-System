# templates/projects/

Scaffolds for per-project files. `/start-project` and `/prd-generator` read from here when creating a new project workspace.

## Files alongside this README

| File | Used by | Purpose |
|---|---|---|
| `brief.md` | `/start-project` | Problem statement, goals, scope, success criteria. Copied into a new project as the starting point. |
| `pm-notes.md` | `/start-project` | PM scratchpad. Hypotheses, decisions, open questions. |
| `input-references.md` | `/start-project` | Linked inputs and feature context. Filled in over time via `/import-inputs` plus manual edits. |
| `prd.md` | `/prd-generator` | The PRD scaffold. Includes the canonical Confluence/Jira frontmatter and section structure (Problem & Goal, Requirements, Acceptance Criteria, Risks, etc.). |
| `competitor-analysis.md` | `/competitive-analysis` | Per-project competitor research scaffold. |

## Editing these templates

- Changes apply to **future** projects, not past ones.
- Keep frontmatter schemas in sync with `CLAUDE.md` and `inputs/SCHEMA.md`. If you add a field to `prd.md`'s jira block, update CLAUDE.md too.
- If you find yourself wanting *per-project* changes, fix the project's local copy instead.

## Adding new templates

If a new template would be reused across many projects (say, `discovery-readout.md` or `launch-checklist.md`), add it here and update whichever skill should copy it.
