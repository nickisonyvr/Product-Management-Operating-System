# templates/

Reusable scaffolds that skills copy from when creating new project files, input files, or Lovable prompts. **Templates are inputs to skills, not files you edit per project.**

## Subfolders

- **`projects/`** — Per-project file templates. `/start-project` copies `brief.md`, `pm-notes.md`, `input-references.md` into a new project workspace. `/prd-generator` uses `prd.md` as the PRD scaffold. `/competitive-analysis` uses `competitor-analysis.md`.
- **`inputs/`** — Per-input-type templates. `/add-input` picks one based on detected input type (customer feedback, meeting notes, competitor analysis).
- **`lovable/`** — A pattern for saving Lovable credits by building a shared app shell once and duplicating per feature. See `lovable/README.md`.

## How to use

You almost never read or copy templates directly. Instead:

- Need a new project? Run `/start-project <Project Name>`.
- Need to file an input? Run `/add-input`.
- Need a PRD? Run `/prd-generator` from inside a project workspace.

If you do edit a template, that change applies to *all future* projects/inputs created from it. Past projects keep the version they were created from — they're independent copies.

## When to edit

- A template field is wrong or missing for your workflow.
- You want every new PRD to include a section that's currently optional.
- Your input frontmatter conventions evolve (then update `inputs/SCHEMA.md` too).

Don't edit templates to fix a single project's needs — fix the project's local copy instead.
