You are publishing a PRD to Confluence. Follow these steps:

## Step 0: Read PM OS Config

Read `pm-os.config.yml` from the repo root if it exists. Note these defaults:

- `atlassian.domain` — full host (e.g., `yoursite.atlassian.net`); used for example URLs in messages back to the user.
- `confluence.default_space_id` — used when a PRD's `confluence.space_id` is empty.
- `confluence.default_parent_id` — used when a PRD's `confluence.parent_id` is empty (optional; safe to skip if both are empty).

If `pm-os.config.yml` doesn't exist, treat all defaults as empty.

## Step 1: Validate File

1. Read the current file (or ask user which file to publish)
2. Parse the YAML frontmatter
3. Check if `confluence.page_id` already exists:
   - If YES: Stop and say "This document is already published. Use /update-on-confluence to update it."
   - If NO: Continue

## Step 2: Prepare Content

1. Extract the title from the first `# Heading` in the markdown
2. Get the markdown content (everything after frontmatter)

## Step 2.5: Filter Content for Confluence

**IMPORTANT:** Before publishing to Confluence, filter the content to remove internal/working sections. The local .md file is NEVER modified — only the content sent to Confluence is filtered.

Apply all filters defined in `docs/confluence-content-filtering.md` (Filter 1: Remove Metadata Block, Filter 2: Process Related PRDs, Filter 3: Resolve Internal Links).

## Step 3: Get Confluence Details

1. Resolve `space_id` with three-tier precedence (config-default + frontmatter-override pattern):
   - **Frontmatter first:** if `confluence.space_id` in the PRD is non-empty, use that. Remember source = "frontmatter".
   - **Config fallback:** else if `confluence.default_space_id` in `pm-os.config.yml` is non-empty, use that. Remember source = "config".
   - **Prompt last:** else ask the user "What is your Confluence space_id? Either set it in the PRD frontmatter, or set `confluence.default_space_id` in `pm-os.config.yml`, or provide it now." Remember source = "prompt".
2. Resolve `parent_id` with three-tier precedence — but never prompt (it's optional):
   - **Frontmatter first:** if `confluence.parent_id` is non-empty, use it (PRD becomes a child page).
   - **Config fallback:** else if `confluence.default_parent_id` in `pm-os.config.yml` is non-empty, use that.
   - **Skip last:** else omit `parent_id` from the API call — the page becomes top-level.
3. Get cloudId by calling `getAccessibleAtlassianResources`

## Step 4: Publish to Confluence

Call `createConfluencePage` with:
```json
{
  "cloudId": "<from step 3>",
  "spaceId": "<from frontmatter or user>",
  "title": "<extracted title>",
  "body": "<FILTERED markdown content with resolved links from Step 2.5>",
  "contentFormat": "markdown",
  "parentId": "<from frontmatter if specified, otherwise omit>"
}
```

**IMPORTANT:** Use the filtered content from Step 2.5, NOT the original file content.

## Step 5: Update Frontmatter

After successful publish, write **only the output field** back to the file's YAML frontmatter:

1. Extract `page_id` from the API response (usually in `id` field)
2. Update only `confluence.page_id`:
   ```yaml
   confluence:
     page_id: "<returned page_id>"
   ```
3. **Do NOT write `space_id` or `parent_id` back to the PRD frontmatter.** Whatever values were originally in `confluence.space_id` / `confluence.parent_id` (empty if they came from config, or hand-set overrides) stay untouched. Writing the resolved values back would convert every config-defaulted PRD into a frontmatter override on first publish — defeating the whole point of `pm-os.config.yml` defaults.
4. Save the file

## Step 6: Confirm to User

Show success message with:
- Title: [document title]
- Page ID: [page_id]
- URL: [construct from response or show from _links.webui]

## Error Handling

- If MCP call fails: Show error and suggest checking permissions
- If file has no frontmatter: Show error and suggest adding it from template
- If space_id is invalid: Show error and ask user to verify
- If already published: Direct to /update-on-confluence command
