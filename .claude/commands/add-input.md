You are filing a raw input into the PM OS. This command handles any type of customer interaction. Follow these steps carefully.

## Step 1: Accept the Raw Input

The user will provide content in one of these forms:

- **Pasted transcript** — Raw text from a call, recording, or meeting
- **Pasted meeting notes** — Bullet points, structured notes, or free-form notes
- **Pasted message thread** — Slack, email, or Teams conversation
- **Brief description** — A few sentences summarizing feedback or a conversation
- **Structured feedback** — Customer name + what they said

Accept whatever the user provides. Do NOT ask them to reformat it.

## Step 2: Classify the Input Type

Based on the content, classify it as one of:

| Type | Signals | Files to |
|------|---------|----------|
| `customer-feedback` | Customer name + opinion/request/complaint about the product | `customer-feedback/` |
| `meeting-notes` | Attendees, agenda, decisions, action items, internal discussion | `meeting-notes/` |
| `transcript` | Long verbatim text, speaker labels, timestamps, call recording | `transcripts/` |
| `competitor-analysis` | Competitor names, feature comparisons, market positioning | `competitor-analysis/` |
| `secondary-research` | Market data, industry reports, external research | `secondary-research/` |

**Mixed content rule:** If the input contains both a transcript AND extractable customer feedback, create TWO files:
1. The full transcript -> `transcripts/`
2. Extracted feedback -> `customer-feedback/`

Tell the user you're creating both and why.

## Step 3: Extract Metadata

Analyze the content and extract as much metadata as possible. Do NOT ask the user for fields you can infer from the text.

**Always extract (infer from content):**
- **Date** — When did this happen? Look for dates in the text, or default to today
- **Customers** — Customer/company names mentioned
- **Features** — Product areas discussed. Use the canonical feature slugs defined in `inputs/SCHEMA.md`.
- **Sentiment** — Overall tone (positive, negative, neutral, mixed, urgent)
- **Priority** — Based on urgency signals, revenue risk, customer size (critical, high, medium, low)

**Extract if present (don't ask if missing):**
- **Attendees/participants** — For meetings and calls
- **Source** — call, email, slack, meeting, jira, support-ticket, gong, survey, sales
- **Customer segment** — enterprise, mid-market, smb (infer from company name if known)
- **Contact name and title** — Who gave the feedback
- **Industry** — Customer's industry

**Only ask the user if:**
- You genuinely cannot determine the customer name
- You cannot determine the feature area (and it matters for filing)
- The content is too ambiguous to classify (Step 2)

Ask at most 2-3 targeted questions, never a full form. Frame them as: "I'm going to file this as [type] for [customer] about [feature]. A few quick questions: ..."

## Step 4: Determine Filing Location

1. Parse the date (from content or default to today)
2. Determine the quarter (Jan-Mar = Q1, Apr-Jun = Q2, Jul-Sep = Q3, Oct-Dec = Q4)
3. Target directory: `inputs/{year}-{quarter}/{type}/`
4. Create the directory if it doesn't exist

## Step 5: Generate Filename

Format: `{customer-or-topic-slug}-{feature-or-subject-slug}.md`

Rules:
- Lowercase, hyphenated slugs
- For transcripts: `{customer-slug}-{topic}-transcript.md`
- For meeting notes: `{topic-slug}-{date}.md`
- For customer feedback: `{customer-slug}-{feature-slug}.md`
- If file exists for same customer+feature: append a new dated section rather than creating a duplicate

## Step 6: Create the File

Use the appropriate template from `templates/inputs/`:

| Type | Template |
|------|----------|
| `customer-feedback` | `templates/inputs/customer-feedback.md` |
| `meeting-notes` | `templates/inputs/meeting-notes.md` |
| `transcript` | No template — use the structure below |
| `competitor-analysis` | `templates/inputs/competitor-analysis.md` |
| `secondary-research` | No template — use the structure below |

### For transcripts (no template):

```yaml
---
date: {YYYY-MM-DD}
type: transcript
customers: [{Customer Name}]
features: [{feature-areas}]
source: {call/zoom/teams/recording}
participants: [{names}]
priority: {level}
tags: [{relevant tags}]
---
```

```markdown
# Transcript: {Customer/Topic} - {Date}

## Key Takeaways
- {Extracted insight 1}
- {Extracted insight 2}
- {Extracted insight 3}

## Participants
{List of participants and roles}

## Full Transcript
{The raw transcript text}
```

### For secondary research (no template):

```yaml
---
date: {YYYY-MM-DD}
type: secondary-research
features: [{feature-areas}]
source: {article/report/analysis}
tags: [{relevant tags}]
---
```

```markdown
# {Research Title}

## Source
{URL or reference}

## Key Findings
- {Finding 1}
- {Finding 2}

## Implications
- {Implication 1}
- {Implication 2}

## Raw Content
{The research content}
```

### For customer feedback and meeting notes:

Follow the existing templates but fill them in using the extracted metadata. For any field you couldn't infer, use `TBD`.

### Content quality rules:

- **Preserve verbatim quotes** — Always keep exact customer quotes when available
- **Extract key takeaways** — Summarize the top 3-5 insights at the top of the file
- **Tag smartly** — Include customer segment, source, feature areas, and contextual tags (e.g., `renewal-risk`, `competitive-threat`, `feature-request`, `bug-report`)
- **Don't lose raw content** — Always include the original text in a "Raw Notes" or "Full Transcript" section at the bottom

## Step 7: Cross-Reference with Projects

Search for related active projects:

1. List all project directories at `projects/*/` (one level under `projects/`)
2. Read `brief.md` files and check for matching feature areas, customer names, or problem themes
3. If related projects exist, tell the user and offer to add a reference to the project's `input-references.md`
4. If the user agrees, append the reference link

## Step 8: Confirm

Show a summary:

```
Input filed successfully!

Type: {customer-feedback / meeting-notes / transcript}
File: inputs/{year}-{quarter}/{type}/{filename}.md
Customer: {name or N/A}
Features: {areas}
Priority: {level}
Tags: {list}

{If cross-referenced}
Linked to: projects/<Project Name>/input-references.md

{If two files created}
Also created: inputs/{year}-{quarter}/{other-type}/{other-filename}.md
```

## Error Handling

- **Ambiguous content**: If you truly can't classify, ask: "This looks like it could be [type A] or [type B]. Which fits better?" — never more than one question
- **No customer name**: File without it — not all inputs are customer-specific (e.g., secondary research, internal meetings)
- **Very long content**: For transcripts >500 lines, still include the full text but put extra effort into the Key Takeaways summary
- **Multiple customers in one input**: Create one file and list all customers in the frontmatter `customers` array
- **Duplicate detection**: If a file for the same customer+feature already exists in the same quarter, append a new dated section rather than creating a duplicate
