# Confluence Content Filtering Rules

When publishing or updating a PRD on Confluence, the content is filtered before being sent. The local `.md` file is **NEVER** modified — only the content sent to Confluence is filtered.

---

## Filter 1: Remove Metadata Block After Title

Remove lines that appear immediately after the title with this pattern:
- Lines starting with `**Product:**`, `**Owner:**`, `**Date:**`, `**Status:**`, `**Last Updated:**` (case-insensitive)
- Any `---` separator following these metadata lines

**Example — Remove this block:**
```markdown
# PRD Title

**Product:** [Your Product] - [Feature Area]  
**Owner:** [Owner name]  
**Date:** [Creation date]  
**Status:** Draft  
**Last Updated:** [Last updated date]

---
```

**Result — Keep only:**
```markdown
# PRD Title

---
```

---

## Filter 2: Process Related PRDs Section

Transform the "Related PRDs" section to only include published documents:

**Rules:**
1. For each markdown link in the "Related PRDs" section:
   - Read the target file's frontmatter
   - If target has `confluence.page_id`: Convert link to Confluence URL (keep the line)
   - If target has NO `confluence.page_id`: **Remove that entire line**
2. If a subsection (e.g., "Dependencies:" or "Context:") has no remaining links: Remove the subsection header
3. If the entire "Related PRDs" section has no remaining links: Remove the entire section including the `## Related PRDs` heading

**Example:**

Before filtering:
```markdown
## Related PRDs

**Dependencies:**
- [Published PRD](./prd-published.md) - Has page_id
- [Unpublished PRD](./prd-not-published.md) - No page_id

**Context:**
- [Another Unpublished](./other.md) - No page_id
```

After filtering (assuming only first PRD is published):
```markdown
## Related PRDs

**Dependencies:**
- [Published PRD](https://site.atlassian.net/wiki/spaces/PROD/pages/123456) - Has page_id
```

If ALL linked PRDs are unpublished: Remove the entire "## Related PRDs" section.

---

## Filter 3: Resolve Remaining Internal Links

After filtering, resolve any remaining internal markdown links:
- Find all markdown links like `[Text](./other-prd.md)`
- For each link, read the target file's frontmatter
- If target has `confluence.page_id`, construct Confluence URL and replace the link
- If target has no page_id, leave link as-is (these would be links outside the "Related PRDs" section)
