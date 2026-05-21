# Input Frontmatter Schema

Canonical values for YAML frontmatter in `inputs/**/*.md` files. Referenced by `/add-input`, `/import-inputs`, and `/start-project`.

---

## Required Fields

| Field | Type | Values |
|-------|------|--------|
| `date` | string | `YYYY-MM-DD` — when the feedback was received or meeting occurred |
| `type` | enum | See [Input Types](#input-types) below |

## Optional Fields

| Field | Type | Values |
|-------|------|--------|
| `customers` | array | Company names, e.g. `[Acme Corp, Globex]` |
| `features` | array | Feature area slugs — see [Feature Slugs](#feature-area-slugs) below |
| `priority` | enum | `critical` · `high` · `medium` · `low` |
| `sentiment` | enum | `positive` · `negative` · `neutral` · `mixed` · `urgent` |
| `source` | enum | `call` · `email` · `slack` · `meeting` · `jira` · `support-ticket` · `gong` · `survey` · `sales` |
| `tags` | array | Free-form strings — see [Common Tags](#common-tags) for conventions |

---

## Input Types

Determines the filing subdirectory under `inputs/{YYYY}-{QN}/`.

| Type | Subdirectory | Use for |
|------|-------------|---------|
| `customer-feedback` | `customer-feedback/` | Customer opinions, requests, complaints about the product |
| `meeting-notes` | `meeting-notes/` | Internal or external meeting notes with attendees, decisions, action items |
| `transcript` | `transcripts/` | Verbatim text from sales calls, recordings, interviews |
| `competitor-analysis` | `competitor-analysis/` | Competitor research, feature comparisons, market positioning |
| `secondary-research` | `secondary-research/` | Market data, industry reports, external research |

## Feature Area Slugs

Define your own canonical feature slugs here. Use kebab-case (e.g. `user-onboarding`, not `User Onboarding`). The list below is illustrative — replace with your product's features.

| Slug | Product Area |
|------|-------------|
| `auth` | Authentication, sign-up, login |
| `onboarding` | First-run experience and setup flows |
| `billing` | Pricing, subscriptions, invoicing |
| `reporting` | Dashboards, analytics, exports |
| `integrations` | Third-party integrations |
| `admin` | Admin settings, permissions, team management |

## Common Tags

Tags are free-form, but these conventions keep things searchable:

| Tag | When to use |
|-----|-------------|
| `enterprise` / `mid-market` / `smb` | Customer segment |
| `feature-request` | Customer is requesting new functionality |
| `bug-report` | Customer reporting a defect |
| `renewal-risk` | Feedback tied to renewal or churn concern |
| `competitive-threat` | Competitor mentioned as alternative |
| `expansion-opportunity` | Upsell or cross-sell signal |

---

## Filing Location

Files are organized by quarter: `inputs/{YYYY}-{QN}/{type}/`

| Months | Quarter |
|--------|---------|
| Jan–Mar | Q1 |
| Apr–Jun | Q2 |
| Jul–Sep | Q3 |
| Oct–Dec | Q4 |

Example: Feedback from Feb 13, 2026 → `inputs/2026-Q1/customer-feedback/`
