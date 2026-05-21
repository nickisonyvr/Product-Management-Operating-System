# projects/

One folder per project: `projects/<Project Name>/`. Flat layout — do not nest projects by product, team, or quarter.

## Project workspace contents

After running `/start-project <Project Name>`, the folder contains:

- **`brief.md`** — Problem statement, goals, scope, success criteria.
- **`pm-notes.md`** — Your scratchpad: hypotheses, open questions, decisions made and why.
- **`input-references.md`** — Links to relevant inputs (filed under `inputs/`) and to product/feature context files.
- **`prd.md`** *(after `/prd-generator`)* — The actual PRD.
- Optional, added as needed: `solution-design.md`, `competitor-analysis.md`, `wireframes.md`.

## Lifecycle

Projects move through stages. There's no enforced state field; track the stage in `pm-notes.md` if it matters.

1. **Discovery** — Problem is fuzzy. Brainstorm with `/product-brainstorming`. Pull relevant inputs with `/import-inputs`.
2. **Definition** — Evaluate solution approaches with `/solution-architect`. Write the PRD with `/prd-generator`.
3. **Development** — Link to Jira via `/publish-to-jira`. Publish to Confluence via `/publish-to-confluence`. Keep the PRD updated with `/update-on-confluence`.
4. **Launched** — Project is shipped. Folder can stay where it is — there's no required archive step.

## Why flat?

Two reasons:

- **Cross-product projects exist.** A project that spans multiple products doesn't fit cleanly under any single product folder.
- **Project names are usually unique.** If you start naming projects the same thing across quarters, that's a signal to add a date or descriptor to the name — not to bucket them.

## Conventions

- **Title-case folder names** with spaces are fine: `projects/Search Relevance Rework/`.
- **One project = one initiative**. If "search" and "search relevance" are two distinct workstreams, make them two folders.
- **Don't edit files in `templates/projects/`** when working on a project. `/start-project` copies templates into the workspace; edit your local copies.
