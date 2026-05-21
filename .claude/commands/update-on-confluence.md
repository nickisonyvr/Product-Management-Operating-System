You are updating a PRD on Confluence. Follow these steps:

## Step 0: Read PM OS Config

Read `pm-os.config.yml` from the repo root if it exists. Note `atlassian.domain` (full host, e.g., `yoursite.atlassian.net`) — used for example URLs in messages back to the user. If `pm-os.config.yml` doesn't exist, treat it as empty.

This command only updates an already-published page (identified by `page_id` in frontmatter), so no other config fields are needed.

## Step 1: Validate File

1. Read the current file (or ask user which file to update)
2. Parse the YAML frontmatter
3. Check if `confluence.page_id` exists:
   - If NO: Stop and say "This document hasn't been published yet. Use /publish-to-confluence first."
   - If YES: Continue with the page_id

## Step 2: Prepare Content

1. Extract the title from the first `# Heading` in the markdown
2. Get the markdown content (everything after frontmatter)

## Step 2.5: Filter Content for Confluence

**IMPORTANT:** Before updating on Confluence, filter the content to remove internal/working sections. The local .md file is NEVER modified — only the content sent to Confluence is filtered.

Apply all filters defined in `docs/confluence-content-filtering.md` (Filter 1: Remove Metadata Block, Filter 2: Process Related PRDs, Filter 3: Resolve Internal Links).

## Step 3: Get Confluence Details

1. Get page_id from frontmatter (validated in Step 1)
2. Get cloudId by calling `getAccessibleAtlassianResources`

## Step 4: Update on Confluence

Call `updateConfluencePage` with:
```json
{
  "cloudId": "<from step 3>",
  "pageId": "<from frontmatter>",
  "body": "<FILTERED markdown content with resolved links from Step 2.5>",
  "contentFormat": "markdown",
  "title": "<extracted title - optional>"
}
```

**IMPORTANT:** Use the filtered content from Step 2.5, NOT the original file content.

## Step 5: Confirm to User

Show success message with:
- Title: [document title]
- Page ID: [page_id from frontmatter]
- URL: [construct URL or get from response]

## Step 6: Optional - Check for Related Links

After updating, optionally suggest which linked documents are published vs not, and offer to update those as well.

## Error Handling

- If no page_id in frontmatter: Direct to /publish-to-confluence command
- If page not found in Confluence: Suggest the page may have been deleted, offer to republish
- If MCP call fails: Show error and suggest checking permissions
- If file has no frontmatter: Show error and suggest adding it from template
