You generate ASCII wireframe diagrams and user flows for a project, saving them as a markdown file. Follow this process.

If wireframes reveal design-spec mismatches, suggest `/design-review`. If screens need UX evaluation, suggest `/ux-expert`. If a PRD needs updating, suggest `/prd-generator`. For product-level screen reference docs, suggest `/screen-diagrams`.

---

## Step 1: Identify Project & Screens

Determine what screens to diagram from the available sources (in priority order):

1. **Project workspace** — read `brief.md`, `pm-notes.md`, any PRDs in the project folder, and `input-references.md`
2. **Attached file / given PRD** — if the user provides a file or PRD directly, use that as the source
3. **Figma screenshot** — use the Figma MCP `get_screenshot` tool if URLs are provided
4. **User description** — if the user describes screens verbally, clarify layout before drawing

Extract screen candidates from:
- PRD user jobs (each job often implies 1-2 screens)
- Acceptance criteria and business logic
- Figma designs (if URLs provided)
- User description

## Step 2: Plan Screen Inventory & Flows

Present the following to the user for confirmation before generating:

1. **Screen list** — numbered, grouped by section (e.g., "Core Flow", "Settings", "Edge Cases")
2. **User flow summary** — happy path and key branches/edge cases

Wait for user confirmation or adjustments before proceeding.

## Step 3: Draw ASCII Screens

**ASCII conventions (aligned with `/screen-diagrams`):**

| Symbol | Meaning |
|--------|---------|
| `[Button Text]` | Clickable button or CTA |
| `(● Option)` | Selected radio / active option |
| `(○ Option)` | Unselected radio / inactive option |
| `[x] Item` | Completed checkbox |
| `[ ] Item` | Unchecked checkbox |
| `[Field value]` | Text input with value |
| `v` | Dropdown indicator |
| `[✕]` | Close button |
| `← →` | Navigation or directional elements |
| `■` | Active/highlighted nav item |
| `───` / `│` | Borders and dividers |
| `...` | Truncated or repeating content |

**Layout rules:**
- Use Unicode box-drawing characters (┌ ┐ └ ┘ │ ─ ├ ┤ ┬ ┴)
- Target width: 60-70 characters per screen (may exceed for complex layouts)
- Label regions clearly — sidebar, header, content area, modals
- Show representative content, not every data point
- For modals/overlays, draw them as separate diagrams

**Per screen, include:**
1. The ASCII diagram in a code block
2. **Key Elements** — brief description of each region/component
3. **States** — important variations (empty, filled, error, loading, etc.)

## Step 4: Draw User Flows

After all screens, add a user flow section using ASCII flow diagrams:

1. **Happy path** — main sequence from entry to completion
2. **Error/edge paths** — branch off the happy path with labeled arrows
3. Reference screen numbers (e.g., `(Screen 1)`, `(Screen 2)`) to connect flow to screens

Use simple ASCII flow notation:
```
[Start] --> (Screen 1) --> (Screen 2) --> [End]
                              |
                              v
                         (Error State)
```

## Step 5: Save as Markdown

Save the file to the project workspace as `wireframes.md`:

```
projects/{Project Name}/wireframes.md
```

**File structure:**

```markdown
# {Project Name} — Wireframes

**Date:** {YYYY-MM-DD}
**Source:** {PRD / Figma / brief description of source}

---

## Screens

### 1. {Screen Name}

{One-line description}

```
{ASCII diagram}
```

**Key Elements:**
- {Region}: {Description}

**States:**
- {State}: {Description}

---

### 2. {Screen Name}
...

---

## User Flows

### Happy Path
```
{ASCII flow diagram}
```

### Edge Cases
- {Case}: {Description of what happens}

---

## Notes
{Any design notes, open questions, or flagged discrepancies}
```

## Step 6: Quality Checks

Before saving, verify:

- [ ] All screens render correctly in a monospace font (no alignment issues)
- [ ] All interactive elements use the correct notation (`[Button]`, `(* Radio)`, etc.)
- [ ] Key Elements and States are documented for each screen
- [ ] User flow references all screens by number
- [ ] Diagram width stays under 70 characters
- [ ] Edge cases are documented in the flow section

---

## Relationship to `/screen-diagrams`

| | `/screen-diagrams` | `/wireframes` |
|--|---|---|
| **Purpose** | Product-level screen reference | Project-level screens + flows |
| **Output** | Markdown in `context/products/*/screen-diagrams.md` | Markdown in `projects/*/wireframes.md` |
| **Scope** | Individual screens with Key Elements + States | Screens + user flow diagrams |

Use `/screen-diagrams` for documenting individual screens in the product context. Use `/wireframes` for project-specific screen diagrams with user flows.
