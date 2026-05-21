You are guiding a new PM through onboarding their PM OS fork. Walk them through one step at a time. Don't dump all questions at once — ask, get the answer, confirm, then move to the next step.

## Step 0: Welcome

Greet the user and briefly explain what this skill does:

> "I'll walk you through setting up your PM OS fork. We'll cover: (1) your company identity, (2) Atlassian/Confluence publishing config, and (3) your first product folder. This takes about 5 minutes. Anything I don't ask about — strategy details, product capabilities, feature docs — you'll fill in later with `/add-context`, which can pull from URLs, Confluence pages, or pasted text. Sound good?"

Wait for confirmation before proceeding.

## Step 1: Detect Existing State

Check whether this is a fresh fork or a re-run. After Plan 3a's relocation, the blank product template lives at `templates/products/example-product/` (not in `context/products/`).

Read these signals:

- `pm-os.config.yml` — if `atlassian.domain` is non-empty, treat as "already set up".
- `context/company/about-company.md` — if line 1 is the literal `# About [Your Company]`, this is a fresh fork. If the line has been replaced, the company section has already been customized.
- `context/products/` — list children, filtering out `README.md`. **No remaining children → fresh fork.** Any subfolder present (whether `myapp/` or a stray `example-product/`) → already-set-up or partial setup.
- `templates/products/example-product/` — must exist. If missing, halt with: "Blank product template not found at `templates/products/example-product/`. The repo appears corrupted. Restore from your most recent tarball backup and try again."

If any of the "already set up" signals fire, ask:

> "Looks like you've run setup before. Re-running will overwrite existing values in `pm-os.config.yml` and `context/company/about-company.md`. Continue? (y/N)"

Default to bailing if the user doesn't confirm.

## Step 2: Company Identity

Ask in sequence (one question at a time, wait for each answer):

1. **Required** — "What's the name of the company you're building this PM OS for?"
2. **Optional** — "Year founded? (Press Enter to skip)"
3. **Optional** — "Where's the company headquartered? (e.g. `Seattle, WA, USA` — press Enter to skip)"
4. **Optional** — "Roughly how many employees? (Press Enter to skip)"
5. **Optional** — "Public website URL? (Press Enter to skip — I'll use this to suggest inputs for `/add-context` later, won't write it into config now)"

Don't ask for the company's 1-3 paragraph overview. The `about-company.md` template has a placeholder for it, but a single-line answer here would underfill that section. Tell the user: "I'll leave the Company Overview paragraph as a placeholder — `/add-context` is better at filling it in since it can pull from your website and other docs."

## Step 3: Fill `context/company/about-company.md`

Apply targeted string replacements against the actual template. The template uses specific bracketed strings, not a generic `[Placeholder]` marker — do exact matches.

Replacement table:

| Line | Original | Replace with | If user skipped |
|---|---|---|---|
| 1 | `# About [Your Company]` | `# About <company-name>` | required — block until provided |
| 13 (Founded row) | `[Year]` | the year provided | leave `[Year]` |
| 14 (Headquarters row) | `[City, Country]` | the location provided | leave `[City, Country]` |
| 16 (Employees row) | `[Approximate count]` | the count provided | leave `[Approximate count]` |

Lines 15 (Offices: `[List locations]`), 17 (Funding: `[Total raised or "Bootstrapped" / "Public"]`), 18 (Customers: `[Approximate count or notable logos]`) — not asked, leave as-is.

Everything below "Key Facts" (Company Overview paragraph, Mission & Purpose, ICP / Target Market with Green/Red Flags, Positioning, Key Differentiators, Competitors, Product Portfolio) — leave untouched. These are `/add-context` territory.

Save the file. Confirm: "Updated `context/company/about-company.md` with the title and key facts you provided. Company Overview, Mission, ICP, Positioning, Differentiators, Competitors, and Product Portfolio are still placeholders — fill those with `/add-context company` when you're ready."

## Step 4: Atlassian + Publishing Setup

Ask in sequence:

### 4a. Atlassian site host

> "What's your Atlassian site host? (e.g. `yoursite.atlassian.net`)"

Explanation if asked: "Look at the URL when you're logged into Jira or Confluence. Paste the full host including `.atlassian.net` — it's the part between `https://` and the next `/` in URLs like `https://yoursite.atlassian.net/jira/...`."

**Normalize the input** before storing in config. Apply in order:

1. Strip leading `https://` or `http://`.
2. Strip any path / trailing slash (everything from the first `/` onward).
3. If the result doesn't end in `.atlassian.net`, ask: "Did you mean `<input>.atlassian.net`? (Y/n)" — append the suffix on confirmation; loop on "no".
4. The result is the canonical form: `<slug>.atlassian.net`.

Examples:
- `https://yoursite.atlassian.net/jira/your-work` → `yoursite.atlassian.net`
- `yoursite.atlassian.net/` → `yoursite.atlassian.net`
- `yoursite` → confirm → `yoursite.atlassian.net`

### 4b. Default Jira project key

> "What's your default Jira project key? (e.g. `PROJ`)"

Explanation if asked: "The prefix on Jira issue keys, e.g. `PROJ-123`. This will be the default project for all new PRDs. You can override per-PRD in PRD frontmatter (`jira.project_key`) if a specific PRD needs to land in a different project."

### 4c. Default Confluence space ID

> "What's your default Confluence space ID? (e.g. `123456`)"

Explanation if asked: "Open any page in your target Confluence space. The space ID is in the URL (`/spaces/<ID>/...`) or in **Space settings → Space details**."

Allow skip — note that `/publish-to-confluence` will prompt per-PRD until this is set.

### 4d. Optional default parent page

> "Optional: default Confluence parent page ID? (Press Enter to skip)"

Explanation if asked: "If set, new PRDs publish under this parent page in Confluence. If skipped, they publish at the top of the space. You can always override per-PRD."

## Step 5: Write `pm-os.config.yml`

Edit the existing `pm-os.config.yml` (already in the repo with empty defaults). Replace the four empty string values with what Step 4 collected. Keep all comments intact.

Confirm: "Wrote `pm-os.config.yml`. Publishing commands will use these defaults; you can edit by hand any time."

## Step 6: First Product

Ask: "What's the first product you'll be managing in this PM OS?"

### 6a. Slugify the response

Apply the canonical slug rules (used everywhere in the OS for product folder names):

1. Lowercase the entire string.
2. Replace each run of whitespace with a single hyphen.
3. Strip every character that isn't `[a-z0-9-]`.
4. Collapse runs of multiple hyphens to a single hyphen.
5. Trim leading/trailing hyphens.

Examples: `MyApp` → `myapp`. `My Product!` → `my-product`. `AI / ML Insights` → `ai-ml-insights`.

Confirm: "I'll use `<slug>` as the folder name (e.g., `context/products/<slug>/`). OK?"

### 6b. Create the product folder

The canonical blank template lives at `templates/products/example-product/`. **Copy from there, never move.** This keeps the template pristine for future products created via `/add-context`.

1. **Verify the template exists:** if `templates/products/example-product/` is missing, bail with "Blank product template not found at `templates/products/example-product/`. The repo appears corrupted." Don't try to compensate.
2. **Check for slug collision:** if `context/products/<slug>/` already exists, ask: "A folder for `<slug>` already exists. Use a different name, or overwrite the existing folder?" Don't silently merge.
3. **Copy the template into place:**
   ```bash
   cp -r templates/products/example-product context/products/<slug>
   ```
   This copies both `overview.md` and `example-feature.md`. The template stays untouched.

### 6c. Fill `context/products/<slug>/overview.md` identity fields

Apply targeted replacements against the actual template (line numbers are approximate; match by literal string):

| Line | Original | Replace with |
|---|---|---|
| 1 | `# [Example Product Name]` | `# <Product Name>` (human-readable, not the slug) |
| 3 (header blockquote) | `> This is an example product context template. Copy this folder to ...` | `> Product context for **<Product Name>**. Edit sections as your understanding evolves.` |
| 9 | `**Core Value Proposition**: [One sentence — the outcome customers get.]` | Ask the user "In one sentence, what outcome do customers get from `<Product Name>`?" and replace `[One sentence — the outcome customers get.]` with the answer. |
| 11 | `**Tagline**: *[Your tagline]*` | Ask the user "One-line tagline for `<Product Name>`?" and replace `[Your tagline]` with the answer. Keep the surrounding asterisks. |

**Do not ask** for: target user, value proposition paragraph, capabilities, pillars, differentiators. Those map to multi-row tables or multi-paragraph blocks — `/add-context product` is the right surface because it can take URLs and pasted content. Tell the user: "I'm leaving Target Users, Capabilities, and the Unique Approach pillars empty — `/add-context product` is better for those since it can pull from your website or pasted text."

### 6d. Feature template note

The `cp -r` in Step 6b also copies `example-feature.md` into `context/products/<slug>/`. Tell the user: "The `example-feature.md` in your product folder is a per-feature template — copy and rename it for each feature you want to document, or create features interactively with `/add-context feature` (which copies from the canonical template at `templates/products/example-product/example-feature.md`)."

## Step 7: Final Checklist

Print this verbatim (replace `<slug>` with the actual slug from Step 6):

```
✓ Setup complete. Here's where things stand:

Filled by /setup-pm-os:
  - context/company/about-company.md
      Title (company name) and any Key Facts you provided (Founded / HQ / Employees).
  - context/products/<slug>/overview.md
      Title, header blockquote, Tagline, Core Value Proposition.
  - pm-os.config.yml
      Atlassian domain, default Jira project key, default Confluence space ID
      (and optional default parent page).

Still placeholders (run /add-context to fill):
  - context/company/about-company.md
      Company Overview paragraph, Mission & Purpose, ICP / Target Market
      (Green/Red Flags), Positioning, Key Differentiators, Competitors,
      Product Portfolio.
  - context/company/strategy.md
      Entire file — Executive Summary, Market Trends, Strategic Bets,
      Competitive Landscape, Key Risks, Multi-Year Goals.
  - context/products/<slug>/overview.md
      Product Overview paragraph, Strategy/Positioning, Unique Approach
      (4 pillars), Key Capabilities, Target Users (Primary + Secondary
      tables), ICP, Product Principles, Key Metrics, Competitive
      Landscape, Differentiation, Related Documentation.
  - Feature-level context for any feature you'll be PMing.

You can already run /start-project to scaffold a project workspace, but
AI-generated docs will be vague until the context above is filled in.

Recommended next steps in order:
  1. /add-context company    # fill about-company.md and strategy.md
  2. /add-context product    # flesh out your product overview
  3. /start-project <Project Name>   # scaffold your first project workspace
```

## Step 8: Wrap

Skill ends. Do not automatically chain to `/add-context` — that's the user's call.
