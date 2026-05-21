You are scanning for relevant inputs to add to a project. Follow these steps.

## Step 1: Determine Search Criteria

**If a project file is open (file path matches `projects/<Project Name>/...`):**
1. Identify the project folder from the open file path — it's the second path segment under `projects/`.
2. Read `projects/<Project Name>/brief.md`
3. Extract search terms:
   - **Feature areas** from the brief (use the canonical slugs defined in `inputs/SCHEMA.md`)
   - **Customer names** mentioned in the brief
   - **Product area** (the relevant `context/products/<product>/` slug, if mentioned)
   - **Keywords** from the problem statement (e.g., "export", "screening questions")
4. Also read `projects/<Project Name>/input-references.md` to know which inputs are already linked (avoid duplicates)

**If no project file is open:**
1. Ask the user: "Which project should I find inputs for?" and "What feature areas or keywords should I search for?"
2. Read the specified project's `brief.md` and `input-references.md`

**Tell the user** what search terms you're using before searching, e.g.: "Searching inputs for: `<feature-slug>`, `<keyword>`, `<Customer Name>`"

## Step 2: Search Inputs Using Frontmatter Grep

Search efficiently — do NOT read full files. Use grep/search to find matches:

1. Search across all `inputs/**/*.md` files for any of the search terms appearing in the YAML frontmatter fields (`features:`, `customers:`, `tags:`)
2. The search should check approximately the first 20 lines of each file (where frontmatter lives)
3. Collect the file paths of all matches
4. Exclude any files already linked in the project's `input-references.md`

**Search strategy:**
- For each search term, grep across input files
- A file that matches multiple search terms is more relevant
- Include partial matches (e.g., searching "export" should match a file tagged `[reporting, export]`)

## Step 3: Build Candidate List

For each matched file, read only the YAML frontmatter (first ~15 lines) to extract:
- `date`
- `type` (customer-feedback, meeting-notes, transcript, etc.)
- `customers`
- `features`
- `priority`
- `sentiment` (if present)

Present the candidates as a numbered list, sorted by relevance (most matching search terms first), then by date (newest first).

Ask: "Which ones should I add to the project? (all / numbers like '1, 3' / none)"

**If no matches found:** Tell the user: "No matching inputs found for [{search terms}]. You can add inputs manually to `input-references.md` or file new ones with `/add-input`."

## Step 4: Add Confirmed Inputs to input-references.md

For each confirmed input:

1. Determine the appropriate section in `input-references.md` based on the input's `type`:
   - `customer-feedback` -> under `## Customer Feedback`
   - `meeting-notes` -> under `## Meeting Notes`
   - `transcript` -> under `## Meeting Notes` (group with meetings)
   - `competitor-analysis` -> create `## Competitor Analysis` section if it doesn't exist
   - `secondary-research` -> under `## External References`

2. Append a link in this format (relative path from the flat project folder is `../../inputs/...`):
   ```markdown
   - [{Customer/Topic} - {Brief Description}](../../inputs/{year}-{quarter}/{type}/{filename}.md) - {one-line summary from title}
   ```

3. Replace any placeholder text ("No customer feedback referenced yet.") with the new link

## Step 5: Confirm

Show what was added with section breakdown.

## Error Handling

- **No project specified and none open**: Ask the user which project to use
- **Project has no input-references.md**: Create it from `templates/projects/input-references.md` first
- **No frontmatter in input files**: Skip files without YAML frontmatter and note them
- **input-references.md section doesn't exist**: Create the appropriate section header before adding the link
