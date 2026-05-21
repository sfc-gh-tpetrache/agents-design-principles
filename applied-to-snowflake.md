# Applied to Snowflake: Stress-Testing the Design Principles

## Why Snowflake

The design principles in [design-principles-v2.md](design-principles-v2.md) are vendor-neutral. This document maps each principle to specific Snowflake features and explains why Snowflake is a natural fit for the architecture.

The core argument: Snowflake provides the front door, control plane, and foundation layer as an integrated platform. The governance stack — identity, policy, tags, RBAC, observability — is built into the same system where the data lives, the models run, and the agents execute. No stitching required.

---

## Front door and control plane in Snowflake

| Generic concept | Snowflake product | What it does |
|---|---|---|
| **Front door** | Agent Studio / Primary Agent | The user interface where people interact with agents. The Primary Agent will route intent to the right specialist automatically |
| **Control plane** | Snowflake Intelligence + Cortex Code + governance stack | The coordination and governance layer that orchestrates execution and enforces policy |

The control plane is wired to the full governance stack:

- **Orchestration** — tool selection, multi-step execution, delegation chains
- **Skills** — Agent Skills (modular, portable task packages on stages or Git)
- **Identity** — Contacts (owner), Object Tags, Agent ID (upcoming)
- **Policy** — role-based access control, row access policies, immutable session attributes, Agent Policy (upcoming), Restricted Session Scopes (upcoming)
- **Tagging** — Object Tags (risk tier, domain, lifecycle stage)
- **Observability** — AI Observability Events, Cortex Agent Usage History
- **Lifecycle** — Agent Versioning, stage transitions, kill switch

---

## Principle-by-feature mapping

| # | Principle | Snowflake features | Why Snowflake |
|---|---|---|---|
| P1 | Outcomes before agents | Agent spec (orchestration instructions), Agent Skills, Semantic Views (verified queries), Agent Evals (evaluation datasets) | The agent's purpose is defined in its spec, scoped by its skills, and grounded by verified queries in semantic views and evaluation datasets in the agent. Together these provide a built-in mechanism to define what the agent is for and what "correct" looks like — before a single user interacts with it. |
| P2 | No anonymous agents | Contacts (owner), Object Tags (risk tier), Agent ID (upcoming) | Contacts assign a named owner. Object Tags classify risk tier with enforced allowed values. Agent ID (upcoming) will add a unique identity that persists through delegation chains. |
| P3 | One front door, one control plane | Snowflake Intelligence, Cortex Code, Primary Agent, Cortex Agents + governance stack (control plane), Agent Studio | Snowflake Intelligence is the user interface where people interact with agents. The Primary Agent will route intent to the right specialist automatically. Cortex Agents orchestrate execution with governance enforced at every step. Agent Studio is where agents are built, registered, and configured. |
| P4 | Specs as code, or it didn't happen | Git + Snowflake CLI, Agent Versioning, Cortex Agent SQL commands | Agents are SQL objects with full DDL — create, alter, describe, drop. Agent Versioning preserves every spec change. The Snowflake CLI enables Git-based workflows: store specs in a repo, review in PRs, deploy via CI/CD. DESCRIBE AGENT returns the full spec at any point for audit or rollback. |
| P5 | Reuse before build | Horizon Catalog, Internal Marketplace, Semantic Views, Semantic View composability (upcoming), Object Tags | Horizon Catalog and Object Tags make existing agents and semantic views searchable by domain, use case, and data sources — duplicates are discoverable before building. Multiple agents can share the same semantic view today; semantic view composability (upcoming) will enable layering and extending semantic views rather than rebuilding per team. The Internal Marketplace enables cross-account sharing, so reuse scales beyond a single team. |
| P6 | One vocabulary, enforced at launch | Tag Policies (allowed values), Horizon Catalog, Semantic View Tags | Allowed values on tags ensure consistent terminology — a risk tier can only be "high", "low", or "critical", not free text. Tags apply to agents, semantic views, and their attributes. Horizon Catalog makes everything searchable by those tags. One taxonomy, enforced at assignment time. |
| P7 | Least context, least privilege | Role-based access control, row access policies, immutable session attributes, Cortex Analyst, Cortex Search, code execution tool, Restricted Session Scopes (upcoming), Agent Policy (upcoming) | Agents access data through governed retrieval — Cortex Analyst queries semantic views respecting tags and masking, Cortex Search retrieves from scoped indexes. Access control and row access policies limit what the agent can reach. The code execution tool runs in an isolated sandbox, with access limited to data in the current session by default. Restricted Session Scopes and Agent Policy (both upcoming) will add dynamic scoping at runtime. The agent gets what it needs for the current task, not everything its owner has access to. |
| P8 | Gates on inputs, outputs, and approvals | Tag-based masking (incl. on semantic view base tables), Cortex Guardrails, aggregation/projection policies, Agent ID (upcoming), Agent Policy (upcoming) | Tag-based masking protects columns based on tags — applied to base tables, it flows through to semantic views. Cortex Guardrails filter unsafe inputs and outputs. Aggregation and projection policies prevent sensitive derived answers. Agent ID (upcoming) will allow governance policies to mask data when an agent is active. Agent Policy (upcoming) will set hard boundaries on what agents can do at runtime. |
| P9 | Trust is earned and re-earned | Agent Evals, Cortex Guardrails, AI Observability Events | Agent Evals test agent quality before promotion and continuously in production. Results are stored in AI Observability Events and visible in Agent Studio. Cortex Guardrails provide runtime filtering. Quality drift is detectable by tracking eval scores over time. |
| P10 | If you can't see it, you can't trust it | AI Observability Events, Cortex Agent Usage History, Event Tables | Every agent call is logged: which agent ran, who triggered it, what role executed it, what it called (including delegation chains), how much it cost, and what tags were active. AI Observability Events capture the full execution trace. All queryable via SQL. |
| P11 | Lifecycle is mandatory | Object Tags (lifecycle stage), Agent Versioning, ALTER/DROP AGENT, Tasks | Lifecycle stage is tracked as an Object Tag. Agent Versioning preserves the full history. ALTER AGENT modifies a running agent. DROP AGENT is the kill switch. Snowflake Tasks can automate transitions — e.g., moving agents to "deprecated" if eval scores drop. The audit trail outlives the agent. |
| P12 | Pave the road, don't block it | Cortex Code Skills, Agent Skills, MCP servers | Cortex Code Skills embed governed workflows into the developer's tooling — the compliant path is a skill, not a checklist. Agent Skills are modular task packages, reusable across agents. MCP servers connect agents to external tools through a standard protocol. The governed path is pre-built. |

---

## Snowflake Reference Architecture

```
                           +-----------------------+
                           |        Users          |
                           +-----------+-----------+
                                       |
                                       v
                      +----------------+----------------+
                      |           Front Door             |
                      |  Agent Studio          |
                      |  Primary Agent         |
                      +----------------+----------------+
                                       |
                                       v
  +--------------------------------------------------------------------+
  |              Control Plane + Governance      
  |                                                                    |
  |  +------------------+  +------------------+  +------------------+  |
  |  | Cortex Agents    |  | Identity &       |  | Agent Studio     |  |
  |  | Orchestration,   |  | Policy           |  | Build, register, |  |
  |  | tool execution,  |  | Contacts,        |  | configure,       |  |
  |  | delegation,      |  | Object Tags,     |  | version, publish |  |
  |  | session mgmt,    |  | RBAC, RAP,       |  |                  |  |
  |  | MCP servers,     |  | Agent ID         |  |                  |  |
  |  | Agent Skills     |  | (upcoming),      |  |                  |  |
  |  |                  |  | Agent Policy     |  |                  |  |
  |  |                  |  | (upcoming),      |  |                  |  |
  |  |                  |  | RSS (upcoming)   |  |                  |  |
  |  +------------------+  +------------------+  +------------------+  |
  |                                                                    |
  |  +------------------+  +------------------+                        |
  |  | Observability    |  | Lifecycle        |                        |
  |  | AI Observability |  | Agent Versioning,|                        |
  |  | Events, Usage    |  | Object Tags      |                        |
  |  | History, Event   |  | (stage), ALTER/  |                        |
  |  | Tables           |  | DROP AGENT       |                        |
  |  +------------------+  +------------------+                        |
  +--------+--------+--------+--------+--------+--------+-------------+
           |        |        |        |        |        |
           v        v        v        v        v        v
  +---------+-------+--------+--------+-----------+----------+
  | Context | Models| UI &   | Apps & | Specs as  | Developer|
  |         |       | Chat   | Integ- | Code      | Tooling  |
  |         |       |        | rations|           |          |
  +---------+-------+--------+--------+-----------+----------+
  |Semantic |Cortex |Stream- |Teams,  |Git +      |Cortex    |
  |Views,   |LLMs,  |lit,    |Slack,  |Snowflake  |Code      |
  |Cortex   |Guard- |Snowflake|custom  |CLI, CI/CD |Skills,   |
  |Search,  |rails, |Intelli-|apps    |pipelines  |Agent     |
  |Horizon  |Agent  |gence   |via MCP |           |Skills,   |
  |Catalog  |Evals  |        |        |           |MCP       |
  +---------+-------+--------+--------+-----------+----------+

  P1 defines the outcome
  P2-P6 govern how agents are discovered, built, and registered
  P7-P10 govern how agents run and are observed
  P11 governs how agents transition and retire
  P12 is how you build it all — skills, templates, paved roads
```

---

## Sources

- [Powering the Era of the Agentic Enterprise](https://www.snowflake.com/en/blog/agentic-enterprise-control-plane/) — Sridhar Ramaswamy, Snowflake Blog, Mar 18, 2026. Introduces the Agentic Enterprise control plane and Project SnowWork (now Snowflake Intelligence).
- [Your AI Agent Is a Nobody. And That's a Problem.](https://www.snowflake.com/en/blog/ai-agent-identity-governance-enterprise-trust/) — Mike Blandina, Snowflake Blog, Apr 28, 2026. The agent identity problem and governance-by-design. Introduces Agent ID, Agent Policy, and RSS.
- [The Agent Context Layer for Trustworthy Data Agents](https://www.snowflake.com/en/blog/agent-context-layer-trustworthy-data-agents/) — Josh Klahr & Rajhans Samdani, Snowflake Blog, Mar 19, 2026. Semantic models, ontologies, provenance, and operational playbooks as the agent context layer.
- [Support for object tagging in semantic views](https://docs.snowflake.com/en/release-notes/2026/other/2026-05-05-semantic-views-object-tagging) — Snowflake Release Notes, May 5, 2026. Enables tag-based governance on semantic views, dimensions, facts, and metrics.
- [Cortex Guardrails](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-ai-guardrails) — Snowflake Documentation. Model-level filtering of unsafe inputs and outputs (harmful content, prompt injection, PII).
- [Snowflake Horizon Catalog](https://docs.snowflake.com/en/user-guide/snowflake-horizon) — Snowflake Documentation. Universal catalog and governance fabric across clouds, regions, engines, and formats.
- [Cortex Agents](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents) — Snowflake Documentation. The agent runtime: orchestration, tool execution, multi-tenancy, and monitoring.
- [Monitor Cortex Agent requests](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-monitor) — Snowflake Documentation. AI Observability Events and CORTEX_AGENT_USAGE_HISTORY for audit and debugging.
- [Multi-tenancy for Cortex Agents](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-multi-tenancy) — Snowflake Documentation. Immutable session attributes and row access policies for tenant isolation.
