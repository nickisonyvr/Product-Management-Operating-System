You are helping a PM add or enrich a context file. Accept any mix of inputs the user has — URLs, Confluence pages, pasted text, attached files, or just conversation — and synthesize into the target file. Preserve the existing section structure; never rewrite a file wholesale.

## Step 1: Pick Level + Target File

Ask:

> "What level of context are you adding?
>   1. **Company** — `about-company.md` or `strategy.md`
>   2. **Product** — `overview.md` or a feature doc for a specific product
>   3. **Feature** — a feature-level doc (creates a new file if needed)"

Branch on the answer:

### Company

Ask which file: `about-company.md` or `strategy.md`. Read the chosen file as the starting point.

### Product

List `context/products/` subdirectories (excluding `README.md`) and ask which product. If the user says "new product":

1. Ask for the product name; slugify per the canonical rule (lowercase, whitespace → hyphens, strip non-`[a-z0-9-]`, collapse multi-hyphens, trim).
2. **Copy from the blank template** at `templates/products/example-product/`:
   ```bash
   cp -r templates/products/example-product context/products/<slug>
   ```
   Never copy from another live product folder — that would inherit content from a different product. If the template doesn't exist, halt with: "Blank product template not found at `templates/products/example-product/`. The repo appears corrupted."
3. Ask: "Add or update `overview.md`, or create a new feature doc?" — branch into the product overview flow or the feature flow.

For an existing product, ask: "Add or update `overview.md`, or create / update a feature doc?"

### Feature

Ask which product folder (list `context/products/` subdirs, excluding `README.md`). Then:

1. Ask "What's the feature name?" — slugify per the canonical rule.
2. Check whether `context/products/<product>/<feature-slug>.md` already exists:
   - **Exists:** ask "Overwrite, append to, or create with a disambiguated name (e.g., `<slug>-v2`)?" If "overwrite" or "append", proceed to Step 2 with that file as the target. If "disambiguate", change `<feature-slug>` to the new value and continue to the create path.
   - **Doesn't exist (create path):**
     ```bash
     cp templates/products/example-product/example-feature.md \
        context/products/<product>/<feature-slug>.md
     ```
     Always copy from the canonical template — never from a sibling feature in the same product folder.
3. **Initialize identity fields** before the synthesis loop (same template-aware pattern `/setup-pm-os` uses for `overview.md`):

   | Line | Original | Replace with |
   |---|---|---|
   | 1 | `# [Example Feature Name]` | `# <Feature Name>` (human-readable, not the slug) |
   | 3 (header blockquote) | `> This is an example feature context template. Copy + rename ...` | `> Feature context for **<Feature Name>** in the **<Product Name>** product. Linked from project \`input-references.md\` files.` |
   | 9 | `**Core Value**: [One sentence — the outcome the feature delivers.]` | Ask the user "In one sentence, what outcome does `<Feature Name>` deliver?" — if they answer, replace `[One sentence — the outcome the feature delivers.]` with the answer. If they skip, leave the placeholder for Step 3's synthesis to fill. |

   Sections below "Feature Overview" (User Jobs, Key Workflows, Key Concepts / Data Model, Edge Cases / Constraints, Integrations / Dependencies, Related Features, Out of Scope, Related Documentation) stay as placeholders — too rich for an interactive initial-fill. Step 3 synthesizes from user-provided inputs.

4. Save the initialized file, then continue to Step 2.

## Step 2: Gather Input Sources

Ask:

> "What inputs do you have for me to learn from? You can mix any of these:
>   - **URLs** — paste any (company website, blog posts, press releases, public docs). I'll fetch them.
>   - **Confluence pages** — paste Confluence URLs or page IDs. I'll fetch them via the Atlassian MCP if connected.
>   - **Pasted text** — paste large chunks directly into the conversation.
>   - **Files** — attach docs (PDF, markdown, images) to the conversation.
>   - **Just talk to me** — I can ask you questions and write what you tell me.
>
> Paste / mention everything you've got, and tell me when you're done."

Loop until the user signals they're done. For each item, classify and fetch:

- **URL** → use `WebFetch` to retrieve and summarize the relevant content.
- **Confluence URL or page ID** → use `mcp__claude_ai_Atlassian__getConfluencePage` (or `searchConfluenceUsingCql` if only a partial URL is provided).
- **Pasted text** → keep in working memory.
- **File path** → use `Read` (handles markdown / text / PDF / images via Claude Code's built-in support).

If a URL fails (404, auth-walled, robots), report it and continue with the rest. Don't bail on the whole session for one bad URL.

## Step 3: Synthesize Into the Target File

Strategy:

- **Preserve the existing section structure** — do NOT rewrite sections wholesale.
- **Placeholder recognition:** the templates do not use a uniform `[Placeholder]` marker. They use descriptive bracketed strings — `[Year]`, `[City, Country]`, `[1-3 paragraph overview: what your company does...]`, `[Your tagline]`, `[Pillar 1]`, `[Persona]`, etc. Treat any bracketed string `[...]` (including tables where every cell is bracketed) as a placeholder slot. Do not require the literal token `[Placeholder]`.
- For each section that's currently a placeholder, look across all gathered inputs for content that fills it. Synthesize a draft.
- For sections that already have content (user is enriching, not initializing), append or merge rather than overwrite — show the user the diff.
- Be explicit about which input each piece came from ("Pulled from the company website" / "Synthesized from the Q3 strategy doc you shared").
- Never invent facts. If a section has nothing in the inputs, leave the placeholder and note it.
- **Table-shaped sections** (Key Facts, Unique Approach pillars, Target Users, Competitive Landscape) — fill row-by-row, ask the user to confirm each row before moving on. Don't delete rows the user didn't fill; leave them as placeholders.

## Step 4: Show + Iterate

Show the user the proposed updates section by section. Ask: "Look good, or want to revise this section?"

For any section the user wants to revise, switch to conversational mode (Socratic questions) and refine.

## Step 5: Save + Suggest Next

Save the file. Print:

> "Updated `<file path>`.
>
> Sections still empty: `<list>`.
>
> Want to keep filling, or are we done for now?"

If "done", suggest the next file the user should work on:

- After `about-company.md` → suggest `/add-context company` on `strategy.md`.
- After `strategy.md` → suggest `/add-context product` on the user's primary product overview.
- After a product overview → suggest `/add-context feature` for any feature the user is actively PMing.
