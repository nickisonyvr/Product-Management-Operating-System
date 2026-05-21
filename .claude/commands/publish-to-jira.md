You are linking a PRD to Jira. Follow these steps:

## Step 0: Read PM OS Config

Read `pm-os.config.yml` from the repo root if it exists. Note these defaults:

- `atlassian.domain` — full host (e.g., `yoursite.atlassian.net`); used for example URLs in messages back to the user.
- `jira.default_project_key` — used when a PRD's `jira.project_key` is empty.

If `pm-os.config.yml` doesn't exist, treat both defaults as empty.

## Step 1: Validate File

1. Read the current file (or ask user which file to publish)
2. Parse the YAML frontmatter
3. Check that a `jira` section exists in frontmatter:
   - If NO: Stop and say "This file has no jira frontmatter. Add it from the PRD template (templates/projects/prd.md)."
   - If YES: Continue
4. Resolve `project_key` with three-tier precedence (config-default + frontmatter-override pattern):
   - **Frontmatter first:** if `jira.project_key` in the PRD is non-empty, use that. Remember the source = "frontmatter".
   - **Config fallback:** else if `jira.default_project_key` in `pm-os.config.yml` is non-empty, use that. Remember source = "config".
   - **Prompt last:** else stop and ask the user "What Jira project key should I use? Either set it in the PRD frontmatter, or set `jira.default_project_key` in `pm-os.config.yml`, or provide it now." Remember source = "prompt".
5. Extract the title from the first `# Heading` in the markdown
6. Extract the problem statement from the `## Problem & Goal` section (text after `**Problem:**`) — this will be used as the description for the Jira issue

## Step 2: Get Atlassian Cloud ID

Call `getAccessibleAtlassianResources` to get the cloudId. Store it for all subsequent Jira API calls.

## Step 3: Handle Software Issue

Use the project key from `jira.project_key` (e.g., `<YOUR-PROJECT>`).

### 3a: Check existing

If `jira.issue_key` already has a value:
- Say: "Already linked to issue [issue_key]. Skipping issue creation."
- Continue to Step 4

### 3b: Ask user's preference

Ask the user: "Do you want to **create a new issue** in <project_key> or **link an existing one**?"

### 3c: Create new issue

If user chooses to create:

1. Ask: "What issue type? (Epic / Task / Story / Bug)"
2. Call `createJiraIssue` with:
```json
{
  "cloudId": "<from step 2>",
  "projectKey": "<project_key>",
  "issueTypeName": "<chosen type>",
  "summary": "<PRD title>",
  "description": "<problem statement from PRD>"
}
```
3. Store the returned issue key and type. The browse URL will be returned by the Jira API; capture it directly from the response (do not hardcode a domain).

### 3d: Link existing issue

If user chooses to link:

1. Ask: "Enter the <project_key> issue key (e.g., <project_key>-123):"
2. Fetch the issue to validate and get its type:
   Call `getJiraIssue` with:
   ```json
   {
     "cloudId": "<from step 2>",
     "issueIdOrKey": "<user-provided key>",
     "fields": ["summary", "issuetype", "status"]
   }
   ```
3. If the issue exists: confirm to user with "Found: [key] - [summary] ([type])"
4. If the issue doesn't exist: show error and ask for the correct key
5. Store the key, detected type, and URL

## Step 4: Update Frontmatter

After successful linking, update **only the output fields** in the file's YAML frontmatter:

```yaml
jira:
  issue_key: "<project_key>-xxx"
  issue_url: "<browse URL from API response>"
  issue_type: "<Epic|Task|Story>"
  created_at: "<today's date in YYYY-MM-DD>"
```

**Do NOT write `project_key` back to the PRD frontmatter.** Whatever value was originally in `jira.project_key` (empty if it came from config, or a hand-set override) stays untouched. Writing the resolved value back would convert every config-defaulted PRD into a frontmatter override on first publish — defeating the whole point of `pm-os.config.yml` defaults.

Only update fields that were newly set in this run. Preserve any existing values for `issue_key` / `issue_url` / `issue_type` / `created_at` if they were already filled.

## Step 5: Confirm to User

Show a success message with the issue key, type, and URL.

## Error Handling

- If MCP call fails: Show error and suggest checking permissions
- If file has no frontmatter: Show error and suggest adding it from template
- If `project_key` is missing or the project is not accessible: Show error and suggest checking project permissions
- If issue type is invalid: Fetch available types with `getJiraProjectIssueTypesMetadata` and show them
- If `issue_key` already exists: Say "Already linked." and show the existing link
