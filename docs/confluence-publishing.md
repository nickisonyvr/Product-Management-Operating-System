# Confluence Publishing Guide

This repository includes Claude Code commands for publishing PRDs to Confluence with automatic link resolution.

## Quick Start

### 1. Set Your Default Confluence Space (one-time)

Open `pm-os.config.yml` and set your default Confluence space ID:

```yaml
confluence:
  default_space_id: "12345"     # Your Confluence space ID
  default_parent_id: ""         # Optional: default parent page for new PRDs
```

If you ran `/setup-pm-os` already, this is filled in. Edit by hand if you ever need to change it.

### 2. PRD Frontmatter Stays Empty by Default

PRDs created from `templates/projects/prd.md` ship with empty `space_id` and `parent_id`:

```yaml
---
confluence:
  page_id: ""              # Filled by /publish-to-confluence
  space_id: ""             # Optional override of pm-os.config.yml confluence.default_space_id
  parent_id: ""            # Optional override of pm-os.config.yml confluence.default_parent_id
---

# PRD Title

Your content here...
```

The publish command reads `space_id` and `parent_id` from `pm-os.config.yml` when they're empty in the PRD. Leave them empty in 99% of PRDs.

### 3. (Rare) Per-PRD Override

Fill `space_id` or `parent_id` in the PRD frontmatter **only** if you want this specific PRD to publish to a different space or under a different parent than your defaults. Example: an engineering PRD that should land in the Eng-Internal space instead of Product:

```yaml
confluence:
  space_id: "ENG-INTERNAL"   # Override: this PRD goes to a different space
```

The publish command preserves whatever override you set — it never writes resolved input values back to PRD frontmatter, so your override survives across re-publishes.

### Finding Your Confluence Space ID

**Option A: Via Claude Code**
```
Ask AI: "Get my Confluence spaces"
```
AI will call `getConfluenceSpaces` and show you available spaces.

**Option B: From Confluence URL**
The space ID is in your Confluence URLs: `https://yoursite.atlassian.net/wiki/spaces/SPACE_ID/...`

## Publishing Commands

### Publish a New Document

Use `/publish-to-confluence` command:

1. Open the PRD file you want to publish
2. Run command: `/publish-to-confluence`
3. The command will:
   - Check you haven't already published it
   - Extract the title and content
   - **Filter content** (remove metadata, unpublished PRDs, Accessibility section)
   - Resolve any internal links to published PRDs
   - Create the Confluence page
   - Update frontmatter with the page_id
   - Show you the Confluence URL

**Example:**
```
User: /publish-to-confluence

AI: ✅ Published to Confluence!

    Title: Onboarding & Signup System - Overview
    Page ID: 789012345
    URL: https://yoursite.atlassian.net/wiki/spaces/PROD/pages/789012345
    
    The page_id has been saved to the file's frontmatter.
```

### Update an Existing Document

Use `/update-on-confluence` command:

1. Make changes to your PRD locally
2. Run command: `/update-on-confluence`
3. The command will:
   - Read the page_id from frontmatter
   - **Filter content** (remove metadata, unpublished PRDs, Accessibility section)
   - Resolve any internal links
   - Update the Confluence page
   - Confirm the update

**Example:**
```
User: /update-on-confluence

AI: ✅ Updated on Confluence!

    Title: Onboarding & Signup System - Overview
    Page ID: 789012345
    URL: https://yoursite.atlassian.net/wiki/spaces/PROD/pages/789012345
    
    All changes have been published to Confluence.
```

## Link Resolution

When you reference another PRD in your markdown:

```markdown
See [Hiring Manager Onboarding](./prd-onboarding-hiring-manager.md) for details.
```

The commands automatically resolve these links:

**If target is published (has page_id):**
```markdown
See [Hiring Manager Onboarding](https://yoursite.atlassian.net/wiki/spaces/PROD/pages/123456) for details.
```

**If target is not published:**
```markdown
See **Hiring Manager Onboarding** _(not yet published)_ for details.
```

This means all your cross-references become working Confluence links!

## Content Filtering for Confluence

**Important:** When publishing or updating PRDs on Confluence, certain sections are automatically filtered out. Your local `.md` files are never modified - only the content sent to Confluence is filtered.

### What Gets Filtered

The commands automatically remove these sections before publishing:

#### 1. Metadata Block

The metadata lines after the title are removed:

**In your local .md file (kept):**
```markdown
# PRD Title

**Product:** [Your Product] - [Feature Area]  
**Owner:** [Owner name]  
**Date:** [Creation date]  
**Status:** Draft  
**Last Updated:** [Last updated date]

---

## Problem & Goal
...
```

**Published to Confluence (metadata removed):**
```markdown
# PRD Title

---

## Problem & Goal
...
```

#### 2. Unpublished PRDs from Related PRDs Section

Only published PRDs (those with `page_id` in frontmatter) appear in the "Related PRDs" section on Confluence:

**In your local .md file:**
```markdown
## Related PRDs

**Dependencies:**
- [Published PRD](./prd-published.md) - Description
- [Draft PRD](./prd-draft.md) - Not published yet

**Context:**
- [Background Doc](./doc.md) - Also not published
```

**Published to Confluence (only published PRDs shown):**
```markdown
## Related PRDs

**Dependencies:**
- [Published PRD](https://site.atlassian.net/wiki/spaces/PROD/pages/123456) - Description
```

**Special cases:**
- If a subsection (like "Dependencies" or "Context") has no published PRDs, the entire subsection is removed
- If the entire "Related PRDs" section has no published PRDs, the whole section is removed

#### 3. Accessibility Subsection

The "Accessibility" subsection under "Constraints" is removed:

**In your local .md file (kept):**
```markdown
## Constraints

**Technical:**
- Must support 1000+ candidates
- Load within 3-5 seconds

**Accessibility:**
- WCAG 2.1 AA compliance
- Full keyboard navigation
- Mobile responsive
```

**Published to Confluence (Accessibility removed):**
```markdown
## Constraints

**Technical:**
- Must support 1000+ candidates
- Load within 3-5 seconds
```

### Why These Filters?

- **Metadata block:** Internal project tracking info not needed by engineering/design teams viewing PRDs in Confluence
- **Unpublished PRDs:** Prevents broken or confusing links to documents that don't exist in Confluence yet
- **Accessibility:** Standard requirement that doesn't need to be repeated in every published PRD

### What Stays in Your Local Files

All filtered content remains in your local `.md` files. This means:

- You can still track Product, Owner, Date, Status locally
- You can link to draft/unpublished PRDs in your local files
- You can keep Accessibility requirements in your source files

**The filtering only affects what goes to Confluence, not your source files.**

### Testing After Publishing

After publishing, you can verify the filtering worked by:

1. Opening the Confluence page
2. Checking that metadata lines are gone
3. Verifying that only published PRD links appear in "Related PRDs"
4. Confirming that "Accessibility" is not under "Constraints"

## Creating Document Hierarchies

To create parent-child relationships in Confluence:

### Step 1: Publish the Parent

```yaml
---
confluence:
  page_id: ""
  space_id: ""          # Empty = use pm-os.config.yml default
  parent_id: ""         # Empty = top-level page
---
```

Run `/publish-to-confluence` → Get page_id (e.g., "789012345")

### Step 2: Set Parent in Children

Update child PRDs with the parent's page_id:

```yaml
---
confluence:
  page_id: ""
  space_id: ""             # Empty = use pm-os.config.yml default
  parent_id: "789012345"   # Parent's page_id — override of the optional config default_parent_id
---
```

### Step 3: Publish Children

Run `/publish-to-confluence` on each child. They'll be created as child pages under the parent.

**Result in Confluence:**
```
Parent Document
├── Child 1
├── Child 2
└── Child 3
```

## Typical Workflows

### Publishing a Single Document

Prerequisite (one-time): `pm-os.config.yml` has `confluence.default_space_id` set. If you ran `/setup-pm-os`, this is filled in.

1. Create PRD with frontmatter (use template — `space_id` and `parent_id` stay empty).
2. `/publish-to-confluence`
3. Done! `page_id` is now in frontmatter. Original empty `space_id`/`parent_id` are preserved.

### Publishing a Group of Related Documents

1. **Publish parent first:**
   - `/publish-to-confluence` on overview/parent doc
   - Note the page_id from frontmatter

2. **Set parent_id in children:**
   - Update each child's frontmatter with parent_id

3. **Publish children:**
   - `/publish-to-confluence` on each child
   - They'll be created as child pages

4. **Update parent (fix links):**
   - Now children are published with page_ids
   - `/update-on-confluence` on parent
   - Links to children will now resolve to Confluence URLs

### Updating Documents

1. Make changes to local markdown file
2. `/update-on-confluence`
3. Changes are live on Confluence

## Best Practices

### 1. Commit Frontmatter Changes

After publishing, the frontmatter is updated with page_id. Commit this:

```bash
git add projects/MyProject/prd-feature.md
git commit -m "Publish Feature PRD to Confluence"
```

This keeps your repo in sync with Confluence.

### 2. Publish Parents Before Children

For proper hierarchy:
1. Publish parent
2. Update children with parent_id
3. Publish children

### 3. Update Parent After Publishing Children

If your parent doc links to child docs:
1. Publish children first (they get page_ids)
2. `/update-on-confluence` on parent
3. Links will now resolve correctly

### 4. Use Descriptive Titles

The first `# Heading` becomes the Confluence page title. Make it clear and searchable.

### 5. Keep Frontmatter in Git

Never manually delete the page_id from frontmatter - it's your link to the Confluence page!

## Troubleshooting

### "This document hasn't been published yet"

**When updating:** You forgot to publish first.
**Solution:** Use `/publish-to-confluence` instead.

### "This document is already published"

**When publishing:** It already has a page_id.
**Solution:** Use `/update-on-confluence` to update it.

### "Page not found" when updating

**Cause:** Confluence page was deleted but frontmatter still has page_id.
**Solution:** 
1. Remove page_id from frontmatter
2. Use `/publish-to-confluence` to recreate

### Links not resolving

**Cause:** Target document hasn't been published yet.
**Solution:** Publish the target document first, then update the source document.

### Permission errors

**Cause:** Don't have write access to Confluence space.
**Solution:** 
1. Verify the space_id is correct. If you didn't set `space_id` in this PRD's frontmatter, the publish command used `confluence.default_space_id` from `pm-os.config.yml` — check there too.
2. Check Confluence space permissions
3. Ensure Atlassian MCP is configured

## Advanced: Per-PRD Space Override

By default, all PRDs publish to `confluence.default_space_id` from `pm-os.config.yml`. To send a specific PRD to a different space, set `space_id` in that PRD's frontmatter:

```yaml
confluence:
  page_id: ""
  space_id: "INTERNAL"    # Per-PRD override
  parent_id: ""
```

This lets you publish to different spaces based on document type (e.g., internal vs customer-facing) without changing the default for every other PRD.

## Commands Reference

| Command | Purpose | Requires |
|---------|---------|----------|
| `/publish-to-confluence` | Publish new PRD with automatic filtering | `space_id` in PRD frontmatter OR `confluence.default_space_id` in `pm-os.config.yml` |
| `/update-on-confluence` | Update existing PRD with automatic filtering | `page_id` in frontmatter (filled by `/publish-to-confluence`) |

## Atlassian MCP Tools Used

Behind the scenes, the commands use:

- `getAccessibleAtlassianResources` - Get cloud_id
- `getConfluenceSpaces` - List available spaces
- `createConfluencePage` - Publish new document
- `updateConfluencePage` - Update existing document

You don't need to call these directly - the commands handle it!

## Getting Help

- **Template:** See `templates/projects/prd.md` for frontmatter structure
- **Commands:** See `.claude/commands/publish-to-confluence.md` and `.claude/commands/update-on-confluence.md`
- **Ask AI:** "How do I publish this PRD to Confluence?"
