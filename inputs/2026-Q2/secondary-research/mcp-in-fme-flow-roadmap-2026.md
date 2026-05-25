---
date: 2026-05-25
type: secondary-research
features: [mcp-in-fme-flow]
source: jira
priority: high
sentiment: positive
tags: [roadmap, mcp, fme-flow, 2026.3, 2026.4, strategic-initiative]
---

# MCP in FME Flow — 2026.3 & 2026.4 Roadmap Features

## Source
Jira Issues — Safe Software internal (IDEA project)  
Parent initiative: [IDEA-2193](https://safesoftware.atlassian.net/browse/IDEA-2193)  
All issues assigned to: Rylan Maschak | Status: Shaping (except IDEA-2546: Candidate/Blocked)

---

## Key Findings

**2026.3 release (3 features in Shaping):**
- MCP Resources support adds read-only context exposure (docs, schemas, configs, job outputs) to MCP servers — complementing the existing Tools-only model.
- File-based writer resource links close a key gap: workspaces that write output files will automatically return resource links in the MCP response, so AI agents can discover and retrieve the data they produced.
- Flow Projects integration ensures MCP server configurations are preserved on export/import — critical for multi-environment deployments and environment promotion.

**2026.4 release (2 features, one blocked):**
- Structured content (outputSchema) support lets tool definitions declare a JSON Schema contract for responses, enabling machine-readable `structuredContent` alongside human-readable output — making FME tools far more useful in automated agent pipelines.
- Async tool support (Tasks) is **blocked** on the Spring AI MCP library not yet supporting the Tasks primitive. If unblocked, it would let long-running workspaces execute asynchronously with progress reporting, removing the synchronous connection-blocking problem for slow workspaces.

---

## Feature Detail

### IDEA-2404 — Support Resources for MCP Servers (2026.3)
[Jira](https://safesoftware.atlassian.net/browse/IDEA-2404) | Status: Shaping

MCP Resources are a first-class spec primitive for exposing read-only context to MCP clients (Claude, ChatGPT, IDE agents). Currently, FME Flow MCP Servers only support Tools — no standardized way to expose contextual artifacts like workspace docs, schemas, job outputs, or reference files without wrapping them in bespoke tool calls.

**What would be implemented:**
- Allow MCP authors to define Resources for each MCP Server on Flow
- Support `resources/list` (paginated) and `resources/read` (text or base64 blob)
- Optional: `resources/templates/list`, `notifications/resources/list_changed`, `resources/subscribe` + `notifications/resources/updated`
- Metadata and annotations: `title`, `description`, `mimeType`, `size`, `audience`, `priority`, `lastModified`

**Problem solved:** MCP hosts that implement resource pickers expect servers to provide resources; without this, FME MCP Servers feel incomplete vs. the spec-supported experience.

---

### IDEA-2538 — File-based Writers Create MCP Resource Links (2026.3)
[Jira](https://safesoftware.atlassian.net/browse/IDEA-2538) | Status: Shaping

When an FME workspace exposed as an MCP tool writes output to files (CSV, GeoJSON, Shapefile, etc.), the MCP response should automatically include resource links pointing to those output files.

**Problem solved:** AI agents calling file-writing workspaces currently get a "success" message with no way to discover or access the data produced. This is a key gap for the "any workspace that delivers data" story.

---

### IDEA-2521 — Add MCP to Flow Projects (2026.3)
[Jira](https://safesoftware.atlassian.net/browse/IDEA-2521) | Status: Shaping

MCP server configurations defined at the server level should be included in Flow Project packages and preserved on export/import. Inclusion is at the server level (whole server, not individual tools). Workspace dependency management is not in scope.

**Problem solved:** MCP configs are currently lost on export/import cycles, creating friction for teams managing multiple FME Flow instances or promoting configurations between environments (dev → staging → prod).

---

### IDEA-2536 — Structured Content (outputSchema) Support (2026.4)
[Jira](https://safesoftware.atlassian.net/browse/IDEA-2536) | Status: Shaping

Add `outputSchema` support to FME Flow MCP tool definitions. When declared, tool results include `structuredContent` — a machine-readable, schema-validated JSON payload — alongside the existing human-readable `content` array.

**Two authoring modes:**
1. Workspaces designed as MCP tools from scratch — schema defined at authoring time
2. Existing workspaces repurposed as MCP tools — schema inferred or configured after the fact

**Problem solved:** Current responses are unstructured text; agents must parse free-form output. Without outputSchema, FME tools are less useful in automated pipelines where downstream steps depend on a known response shape.

---

### IDEA-2546 — Async Tool Support / Tasks (2026.4 — maybe)
[Jira](https://safesoftware.atlassian.net/browse/IDEA-2546) | Status: Candidate | **BLOCKED on Spring AI MCP library**

⚠️ Currently blocked — Spring AI MCP SDK does not yet support the Tasks primitive. Delivery is contingent on the library adding support and on customer demand validating the investment.

FME workspaces can take minutes or hours to run. Today, MCP tool calls block synchronously until the job completes — risking connection timeouts and leaving agents unable to do other work while waiting. MCP Tasks (experimental in the 2025-11-25 spec) would allow FME Flow to return a durable handle immediately and publish progress updates asynchronously.

**Problem solved:** Without async support, customers are effectively limited to exposing only fast-completing workspaces as MCP tools — significantly narrowing FME Flow's usefulness as an MCP server.

---

## Implications

- The 2026.3 features (Resources, Resource Links, Projects) collectively make MCP in FME Flow spec-complete for the most common enterprise deployment patterns.
- Structured content (2026.4) is the key unlock for agents using FME tools in automated, multi-step pipelines — not just single-turn interactions.
- The Tasks blocker (Spring AI library) is a spec and library dependency risk that needs tracking; if the library ships Tasks support, this should move up the priority list given how important long-running workspace support is for real enterprise workflows.
- Flow Projects integration is particularly important for enterprise customers managing multi-environment FME deployments.
