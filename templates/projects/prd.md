---
confluence:
  page_id: ""              # Filled by /publish-to-confluence
  space_id: ""             # Optional override of pm-os.config.yml confluence.default_space_id
  parent_id: ""            # Optional override of pm-os.config.yml confluence.default_parent_id
jira:
  project_key: ""          # Optional override of pm-os.config.yml jira.default_project_key
  issue_key: ""            # Filled by /publish-to-jira
  issue_url: ""            # Filled by /publish-to-jira
  issue_type: ""           # "Epic", "Task", or "Story"
  created_at: ""           # ISO date when first linked
---

# [Feature/Component Name]

---

## Problem & Goal

**Problem:** [What problem are we solving? Be specific about the pain point.]

**For whom:** [Target user persona - be specific]

**Goal:** [What outcome are we trying to achieve? One clear statement.]

**Success Metrics:**
- [Measurable target 1 — e.g., "70% adoption within 90 days"]
- [Measurable target 2]
- [Measurable target 3]

## Related PRDs

**Dependencies:**
- [PRD Name](./link-to-prd.md) - [Brief description of dependency]
- [Another PRD](./link.md) - [Why it's relevant]

**Context:**
- [Background Doc](./link.md) - [What context it provides]

---

## Requirements

### Job 1: [User job statement - what the user is trying to accomplish]

**What & Why:** [Combine what the feature does and why the user needs it. 1-2 sentences explaining the job this feature solves for the user.]

**Must have:**
- [Specific requirement 1]
- [Specific requirement 2]
- [Specific requirement 3]

**Business Rules:**
- [Rule 1 - e.g., "All users start with 'Pending' status"]
- [Rule 2]

**Edge Cases:**
- [Edge case 1] → [How to handle]
- [Edge case 2] → [How to handle]

---

### Job 2: [Next user job]

**What & Why:** [What + why combined]

**Must have:**
- [Requirements]

**Business Rules:**
- [If applicable]

**Edge Cases:**
- [If applicable]

---

<!-- Add more jobs as needed -->

---

## Constraints

**Design Baseline:**
- [Existing patterns to follow — e.g., "Follow patterns from <related feature in your product>"]
- [Key differences from baseline]
- [Reference link if applicable]

**Business:**
- [Business constraints — e.g., partnership policies, regulatory limits]
- [Business rules that limit design/implementation options]

---

## Acceptance Criteria

*Single verification checklist. Each item is independently testable.
Group by functional area, not user job. Include cross-cutting items
(analytics, error handling, empty states) that don't belong to any single job.*

### [Functional Area 1]
- AC1: [Observable outcome — what a tester can verify]
- AC2: [Another testable statement]

### [Functional Area 2]
- AC3: [Testable statement]

<!-- For split PRDs: parent has cross-cutting ACs (analytics, permissions, perf);
     child docs have their own scope-specific ACs with IDs continuing the sequence. -->

---

## Design Questions

1. **[Question 1]:** [Description of design decision needed]
   - Context: [Why this matters or what to consider]

2. **[Question 2]:** [Description]
   - Context: [Additional context]

3. **[Question 3]:** [Description]

---

## Risks & Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| [Risk 1] | [High/Medium/Low or Critical] | [How we'll handle it] |
| [Risk 2] | [Impact level] | [Mitigation strategy] |
| [Risk 3] | [Impact level] | [Mitigation strategy] |

---

## Open Questions
