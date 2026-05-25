# MCP in FME Flow

> Feature context for **MCP in FME Flow** in the **FME Flow** product. Linked from project `input-references.md` files.

## Feature Overview

MCP in FME Flow enables customers to expose any FME Flow workspace as an MCP-compatible tool, allowing external AI agents to invoke FME workflows using the Model Context Protocol standard. FME Flow acts as a first-class MCP Server — bridging AI agents and enterprise data workflows across hundreds of systems, including legacy platforms that no other MCP-enabled vendor can easily reach.

The core problem: enterprises want AI agents to access internal systems in a secure, centralized, and reusable way, but most internal systems don't ship with MCP servers. FME is uniquely positioned to solve this by wrapping complex, multi-source business logic and data transformation behind a single MCP tool interface — without custom middleware, scripting, or vendor-specific glue code.

**Core Value**: Using FME Flow, customers can build their own MCP servers (with optional authentication) and create tools that run FME workspaces — exposing their data integration workflows as AI-consumable tools.

---

## User Jobs

1. **Expose internal systems to AI agents** — FME admin or solution architect wants to make existing FME workflows callable by AI agents without building custom middleware or duplicating logic across agent platforms.
2. **Make legacy systems AI-accessible** — Integration engineer needs to give AI agents access to file servers, FTP, mainframes, or on-prem tools that will never have native MCP support.
3. **Curate and merge data for agentic access** — Data engineer wants to expose multi-source, transformed data to AI agents through a single MCP tool interface, rather than forcing agents to stitch together raw system queries.
4. **Govern AI agent access centrally** — IT/security lead wants a single, trusted layer for controlling which systems AI agents can access, rather than managing agent-specific plugins per system.

---

## Key Workflows

### Workflow 1: Create an MCP server and register a workspace as a tool

1. User opens FME Flow and navigates to the MCP server builder.
2. Creates a new MCP server (with optional authentication configuration).
3. Selects an existing FME workspace and registers it as an MCP tool.
4. Configures tool name, description, and input parameters exposed to AI agents.
5. MCP server endpoint is generated and ready for AI agent consumption.

### Workflow 2: AI agent invokes an FME workspace via MCP

1. External AI agent connects to the FME Flow MCP server using the MCP protocol.
2. Agent discovers available tools (registered FME workspaces).
3. Agent calls a tool with input parameters.
4. FME Flow runs the workspace and returns the result to the agent.

---

## Key Concepts / Data Model

| Concept | Description |
|---------|-------------|
| **MCP Server** | An MCP-protocol-compliant server hosted in FME Flow. Contains one or more registered MCP tools (workspaces). Optionally secured with authentication. |
| **MCP Tool** | A single FME workspace registered as a callable tool. Has a name, description, and defined input parameters exposed to AI agents via the MCP standard. |
| **Workspace** | An existing FME Flow workflow. The underlying execution unit behind each MCP tool — handles all data connectivity, transformation, and business logic. |
| **AI Agent** | An external LLM-powered agent (e.g. Claude, GPT, custom agent) that connects to the FME Flow MCP server to discover and invoke tools. |

---

## Edge Cases / Constraints

- **MCP protocol immaturity** — The MCP spec is still evolving, particularly around authentication and security. Implementation may need to adapt mid-project as the spec changes.
- **Authentication complexity** — Auth on MCP servers is optional in v1 but critical for enterprise adoption. Spec gaps mean this may require Safe-defined conventions until the standard matures.
- **Spring framework dependency** — MCP in FME Flow depends on a Spring framework upgrade in FME Flow. This dependency is at risk of slipping and could block delivery.
- **Legacy system connectivity** — FME's ability to reach legacy systems (file servers, FTP, mainframes) is a core differentiator but may require customers to have those systems accessible to the FME Flow instance.
- **Setup time target** — Success metric requires customers to be able to create an MCP server and call it from an AI agent in under 10 minutes. UX must be designed to this bar.

---

## Integrations / Dependencies

| System / Feature | Direction | Purpose |
|------------------|-----------|---------|
| **FME Flow Workspaces** | In | The execution layer behind every MCP tool. Any workspace can be registered as a tool. |
| **MCP Protocol (Anthropic standard)** | Both | The protocol FME Flow implements to expose tools and accept agent calls. |
| **External AI Agents** | In | Claude, GPT, custom LLM agents that connect to the FME Flow MCP server to discover and invoke tools. |
| **FME Flow Authentication** | In | Optional auth layer for MCP servers; secures which agents can connect. |
| **Spring Framework (FME Flow internal)** | In | Infrastructure dependency — MCP delivery is blocked until the Spring upgrade in FME Flow is complete. |

---

## Roadmap (Upcoming Features)

### 2026.3
| Feature | IDEA | Description |
|---------|------|-------------|
| **MCP Resources support** | [IDEA-2404](https://safesoftware.atlassian.net/browse/IDEA-2404) | Expose read-only context (docs, schemas, configs, job outputs) to MCP clients via `resources/list` and `resources/read` — complementing the existing Tools-only model. |
| **File-based writer resource links** | [IDEA-2538](https://safesoftware.atlassian.net/browse/IDEA-2538) | Workspaces that write output files automatically return MCP resource links in their response, so AI agents can discover and retrieve produced data. |
| **MCP in Flow Projects** | [IDEA-2521](https://safesoftware.atlassian.net/browse/IDEA-2521) | MCP server configurations are included in Flow Project exports/imports, enabling reliable environment promotion (dev → staging → prod). |

### 2026.4
| Feature | IDEA | Description |
|---------|------|-------------|
| **Structured content (outputSchema)** | [IDEA-2536](https://safesoftware.atlassian.net/browse/IDEA-2536) | Tool definitions declare a JSON Schema output contract; responses include machine-readable `structuredContent` alongside human-readable output for use in automated agent pipelines. |
| **Async tool support / Tasks** *(maybe — blocked)* | [IDEA-2546](https://safesoftware.atlassian.net/browse/IDEA-2546) | Long-running workspace jobs execute asynchronously via MCP Tasks primitive, returning a durable handle immediately and publishing progress updates. **Blocked on Spring AI MCP library support.** |

---

## Related Features

- **FME Flow Data Virtualization** — Previous approach to exposing FME workflows via OpenAPI; MCP is a more powerful and AI-native alternative.
- **MCPCaller Transformer** — Complementary feature in FME Form/Flow for *consuming* MCP tools from within a workspace. MCP in FME Flow is the *server* side (exposing tools); MCPCaller is the *client* side (calling tools).
- **FME Flow Projects** — MCP server configurations will be included in Flow Projects (2026.3), enabling environment promotion.

## Out of Scope

- **Consuming MCP tools inside workspaces** — That's the MCPCaller Transformer, a separate feature.
- **Guaranteed MCP spec stability** — Safe will adapt as the protocol matures; no commitment to a frozen spec version.
- **Non-FME-Flow deployments** — MCP server builder lives in FME Flow; FME Form-only customers are not the initial target.
- **Advanced agent orchestration** — FME Flow exposes the tools; orchestration logic lives in the AI agent, not in FME.

---

## Related Documentation

- [overview.md](./overview.md) — FME Flow product overview
- `../../company/about-company.md` — Company context
- `../../../inputs/2026-Q2/secondary-research/mcp-in-fme-flow-initiative.md` — IDEA-2193 Jira initiative (parent initiative)
- `../../../inputs/2026-Q2/secondary-research/mcp-in-fme-flow-roadmap-2026.md` — 2026.3 & 2026.4 roadmap features (IDEA-2404, 2538, 2521, 2536, 2546)
