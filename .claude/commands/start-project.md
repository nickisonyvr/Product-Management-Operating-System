You are scaffolding a new project workspace. Follow these steps:

## Invocation

`/start-project <Project Name>`

- `<Project Name>` (required, positional) — Human-readable folder name; may contain spaces. Projects live flat under `projects/<Project Name>/` (no buckets).

If `<Project Name>` is missing, stop and respond:
> Usage: `/start-project <Project Name>`

## Step 1: Collect Project Details

The project name comes from the invocation. Ask only for what's missing:

1. **One-line problem statement** (required) — What problem does this project solve?
2. **Known inputs** (optional) — Any existing customer feedback, meeting notes, transcripts, or other inputs to reference.
   - Accept file paths (e.g., `inputs/2026-Q1/meeting-notes/kickoff-notes.md`)
   - Accept descriptions (e.g., "Customer feedback from Acme Corp about export feature")
   - If none yet, that's fine — skip this

If the user provides all details upfront (e.g., in a single message), do NOT re-ask. Extract what you can and proceed.

## Step 2: Validate & Create Project Directory

1. Check if `projects/<Project Name>/` already exists:
   - If YES: Stop and tell the user: "A project with this name already exists at `projects/<Project Name>/`. Choose a different name or work within the existing project."
   - If NO: Continue
2. Create the directory: `projects/<Project Name>/`

**Note:** Project names may contain spaces, hyphens, and special characters. Use the name exactly as the user provides it for the folder name.

## Step 3: Create `brief.md`

Create `projects/<Project Name>/brief.md` using the template from `templates/projects/brief.md`.

Pre-fill the following fields with the information collected in Step 1:

- **Title**: Replace `[Project Name]` with the actual project name
- **Project Name** (in the metadata table): Fill with the project name
- **Created**: Fill with today's date in YYYY-MM-DD format
- **Status**: Set to `Discovery`
- **The Problem** (under Problem Statement): Fill with the one-line problem statement the user provided

Leave all other sections as template placeholders for the user to fill in later.

## Step 4: Create `pm-notes.md`

Create `projects/<Project Name>/pm-notes.md` using the template from `templates/projects/pm-notes.md`.

Pre-fill:

- **Title**: Replace `[Project Name]` with the actual project name
- **Last Updated**: Set to today's date in YYYY-MM-DD format

Leave all other sections as template placeholders.

## Step 5: Create `input-references.md`

Create `projects/<Project Name>/input-references.md` using the template from `templates/projects/input-references.md`.

Pre-fill:

- **Title**: Replace `[Project Name]` with the actual project name
- **Feature Context section**: Suggest feature context files from `context/products/*/` whose filenames or section headings overlap with keywords from the problem statement. Confirm with the user before linking; if no clear match, leave the section as a placeholder.

**If the user provided known inputs in Step 1:**
- Parse each input and place it under the appropriate section (Customer Feedback, Meeting Notes, etc.)
- Use relative paths from the project folder (e.g., `../../inputs/2026-Q1/meeting-notes/kickoff.md`)
- Include a brief description if the user provided one

**If no inputs were provided:**
- Use the placeholder text ("No customer feedback referenced yet.", etc.)

## Step 5.5: Auto-Suggest Relevant Inputs

After creating the project files, scan `inputs/` for files that may be relevant to this project:

1. Build search terms from the project details collected in Step 1:
   - Feature area slugs (from `inputs/SCHEMA.md`)
   - Customer names (if any were mentioned)
   - Keywords from the problem statement
2. Grep across `inputs/**/*.md` for files where `features:`, `customers:`, or `tags:` in the YAML frontmatter match any search term (search the first ~20 lines of each file only — do not read full files)
3. Exclude any inputs the user already provided in Step 1 (already linked)
4. If matches are found:
   - Read only the frontmatter (~15 lines) of each match to extract: date, type, customers, features, priority
   - Present a numbered candidate list and ask which to add to `input-references.md`
   - If user confirms: add the links to `input-references.md` under the appropriate section
5. If no matches found: skip silently and proceed to Step 6

## Step 6: Confirm & Suggest Next Steps

After creating all files (and optionally importing inputs), show the user a confirmation and the recommended skill pipeline:

```
Project workspace created at projects/<Project Name>/

Files created:
  - brief.md — Pre-filled with problem statement. Fill in goals, scope, and metrics.
  - pm-notes.md — Your working scratchpad for hypotheses and decisions.
  - input-references.md — {N} input(s) referenced. Add more as you gather them.

Recommended next steps:
  1. Fill in brief.md with the full problem statement, goals, and scope
  2. Add relevant inputs with /import-inputs (or manually in input-references.md)
  3. When ready to explore the problem deeply -> use the product-brainstorming skill
  4. When ready to define solution approaches -> use the solution-architect skill
  5. When ready to write the PRD -> use the prd-generator skill
  6. When ready to visualize screen flows -> use the wireframes skill
```

## Error Handling

- **Project already exists**: Stop and inform the user (see Step 2)
- **Missing required fields**: If the user doesn't provide a project name or problem statement, ask for them specifically. Do not proceed without these two required fields.
- **Template files missing**: If a template file can't be read, create the file with a minimal structure that follows the same pattern and inform the user.
