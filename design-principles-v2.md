# Design Principles for Scaling Agents Without Sprawl

## Why these principles matter

Enterprises are deploying agents across customer support, finance, sales, and operations faster than they can govern them. The result is **agent sprawl**: dozens of overlapping agents built by different teams, on different stacks, with no shared identity, context, or audit trail.

The bottleneck is no longer model capability — it's **trust**. An agent can act, but accountability is a different question. If something goes wrong six months from now, can you answer who built it, what data it touched, what it produced, and on whose authority? For most organizations today, the answer is no.

These twelve principles prevent sprawl by making the **compliant path the easiest path**: one front door funnels usage, one control plane enforces policy, reuse beats rebuild, and every agent is a named product with a lifecycle and an audit trail that outlives it. Certified metrics and semantic models are the shared language underneath — they ensure agents reason over governed, validated definitions rather than ad-hoc SQL, so every answer is traceable to a trusted source of truth. Together these principles let a central AI team scale agents to the whole enterprise — fast for builders, safe for security, and provable for regulators.


---

## Summary

| # | Principle | One-liner |
|---|---|---|
| P1 | Outcomes before agents | No agent without a purpose and a success metric |
| P2 | No anonymous agents | Identity and risk tier travel everywhere, including through delegation chains |
| P3 | One front door, one control plane | One uber agent for users, one studio for builders, one runtime layer for enforcement |
| P4 | Specs as code, or it didn't happen | Agents, prompts, tools, and policies live in Git |
| P5 | Reuse before build | Check the inventory first; build only what doesn't exist |
| P6 | One vocabulary, enforced at launch | Shared terms for domains, tiers, and statuses — checked at creation and continuously |
| P7 | Least context, least privilege | An agent gets only the data and authority it needs for the current task |
| P8 | Gates on inputs, outputs, and approvals | Policy fires on data in, answers out, and the human sign-off for high-risk actions |
| P9 | Trust is earned and re-earned | Pass a quality bar before go-live; keep passing it in production |
| P10 | If you can't see it, you can't trust it | Every agent emits usage, cost, errors, and traceable provenance |
| P11 | Lifecycle is mandatory | Every agent has defined stages and an emergency stop button |
| P12 | Pave the road, don't block it | The compliant path is the easiest path |

---

## MECE coverage: each principle owns exactly one concern

Every question you can ask about an agent maps to exactly one principle. No gaps, no overlaps.

| # | Principle | Concern | Question it answers |
|---|---|---|---|
| P1 | Outcomes before agents | Why it exists | Should this agent exist at all? |
| P2 | No anonymous agents | Who it is and how risky | What is this agent, who owns it, and what's the blast radius? |
| P3 | One front door, one control plane | Where it lives | Where do users find it, where is it built, where is it enforced? |
| P4 | Specs as code, or it didn't happen | How it's defined | What does this agent look like, and can I prove it? |
| P5 | Reuse before build | How it's found | Does something like this already exist? |
| P6 | One vocabulary, enforced at launch | What words describe it | Are we all talking about the same thing? |
| P7 | Least context, least privilege | What it's allowed to touch | How much data and authority does it actually need? |
| P8 | Gates on inputs, outputs, and approvals | How it's controlled at runtime | What goes in, what comes out, and who approves? |
| P9 | Trust is earned and re-earned | How quality is judged | Is it good enough to launch, and is it still good enough today? |
| P10 | If you can't see it, you can't trust it | How operations are seen | What evidence does it produce? |
| P11 | Lifecycle is mandatory | How it evolves and ends | What stage is it in, and how do we stop it? |
| P12 | Pave the road, don't block it | How it's delivered to builders | Are there skills, templates, and tools that make the right thing the easy thing? |

The table is structured to be MECE: each principle owns exactly one concern, and together they cover every question you need to answer about an agent — from why it exists to how it's retired. If a question doesn't map to a row, a principle is missing. If it maps to two rows, the boundaries need tightening.

---

## Principles

**P1. Outcomes before agents.**
No agent gets registered until someone can say what it's for and how we'll know it worked.

*Why it matters.* Most sprawl starts with agents built because they're interesting, not because they're needed. Without a clear outcome, you can't tell which agents are worth keeping.

*What it prevents.* Demos that become permanent. Fleets nobody can prune. Agents that outlive the problem they solved.

---

**P2. No anonymous agents.**
Every agent has a name, an owner, a purpose, and a risk tier — set at registration. Identity travels with the agent through delegation chains. Sub-agents carry their own identity and never exceed their parent's authority.

*Why it matters.* You can't control what you can't identify. You can't audit a chain when the links are anonymous. And you can't retire what you can't name.

*What it prevents.* Actions nobody can attribute. Sub-agents inheriting more authority than their parent intended. Accountability gaps in multi-agent chains.

---

**P3. One front door, one control plane.**
Users come in through one agent that understands intent and routes to the right specialist. Behind it, one studio where agents are built, registered, governed, and monitored. Between them, one runtime layer that enforces policy, logs every action, and coordinates execution.

*Why it matters.* The front door turns a fleet of specialists into a single experience. The studio gives builders a shared place to create and manage. The runtime layer makes sure nothing runs outside the rules — regardless of how it was called. Without all three, you get gaps: agents that are built but not governed, or governed but not observable.

*What it prevents.* Users hunting for the right agent. Builders working in isolation. Agents that bypass policy at runtime. Actions that are allowed at build time but should have been blocked at execution time.

---

**P4. Specs as code, or it didn't happen.**
Agents, prompts, tools, and policies live in Git. Reviewed, versioned, rollbackable.

*Why it matters.* Prompts drift. When someone asks what the agent looked like last quarter, version control is the difference between a clear answer and guesswork.

*What it prevents.* Unexplained changes to production agents. Rollbacks you can't do. Audits with nothing to show.

---

**P5. Reuse before build.**
Before anyone builds, the inventory comes up first. Build only what doesn't already exist.

*Why it matters.* Duplication is the biggest cause of sprawl, and it's almost always accidental — two teams solving the same problem without knowing about each other.

*What it prevents.* Multiple agents doing the same job. Components nobody knew existed. Discovery that depends on word of mouth.

---

**P6. One vocabulary, enforced at launch.**
A shared set of terms — domains, tiers, statuses, entities — applied to every agent, tool, and view. Checked at creation and validated continuously.

*Why it matters.* If two teams label the same thing differently, search breaks. If risk tiers mean different things in different places, governance doesn't work. Dashboards still render — they just show the wrong picture.

*What it prevents.* Reuse failing because things can't be found. Inconsistent risk tiers. Metrics that don't add up across teams.

---

**P7. Least context, least privilege.**
An agent receives only the data it needs for the current task and only the authority required to complete it. Scope is set dynamically, not inherited from the calling user's full permissions.

*Why it matters.* An agent with access to everything will eventually read something it shouldn't — not out of malice, but because the question was broad. Dynamic scoping keeps the impact of a bad decision small.

*What it prevents.* Routine tasks running with full permissions. A summarization job reading data it didn't need. Broad access granted for convenience and used by accident.

---

**P8. Gates on inputs, outputs, and approvals.**
Policy fires in three places: the data going in, the answer coming out, and the human sign-off required for high-risk actions.

*Why it matters.* Access control on the data alone isn't enough. Two allowed inputs can produce an answer nobody should see. And some decisions need a person — in many jurisdictions, that's a legal requirement.

*What it prevents.* Sensitive answers assembled from individually harmless inputs. Autonomous action where a human should have signed off.

---

**P9. Trust is earned and re-earned.**
Every agent passes a quality and safety bar before it goes live. The same bar is reapplied while it's running.

*Why it matters.* Models change, data changes, and agents change with them. If you only test at launch, you find out something is wrong when a user reports it.

*What it prevents.* Demo-quality agents in production. Gradual drift nobody notices. Issues that compound over months.

---

**P10. If you can't see it, you can't trust it.**
Every agent emits usage, cost, errors, and provenance. Answers are traceable to the tools and data sources used. Cost rolls up to an owner.

*Why it matters.* Trust comes from evidence. If you can't show where an answer came from, you have output but no accountability. If nobody owns the cost, the cost just grows.

*What it prevents.* Answers with no trail. Costs with no owner. Surprise bills.

---

**P11. Lifecycle is mandatory.**
Every agent moves through defined stages: experimental, registered, production, deprecated, retired. Emergency stop is a built-in feature.

*Why it matters.* Agents don't retire themselves. Without a planned end state, they keep running — forgotten, unowned, still touching data. And when something goes wrong, you need a stop button that works now.

*What it prevents.* Orphaned agents that can't be shut down. Slow deprecation during a fast incident. Agents retired with no record of what they did.

---

**P12. Pave the road, don't block it.**
The compliant path is the easiest path. Governance is built into the tools, not added as a separate approval queue.

*Why it matters.* People route around governance that slows them down. Every shadow agent ecosystem started with a governed path that was too hard to use.

*What it prevents.* Shadow agents. Teams building outside the platform. The central system becoming the place that says no.

---

## How the principles work together: the agent lifecycle workflow

The principles aren't a checklist — they fire in sequence as an agent moves from idea to retirement.

```
Someone has a business problem
    |
    v
P1 (Outcomes before agents) — name the outcome before anything else
    |
    v
They ask the front door whether a solution already exists
    P3 (One front door) + P5 (Reuse before build) + P6 (Vocabulary, so the search works)
    |
    v
If reuse fails, they build
    P4 (Specs as code) with P12 (Pave the road) making the compliant build the easy build
    |
    v
They register it
    P2 (Identity + risk tier, with identity travel through chains)
    into P3's catalog
    P7 (Least context, least privilege) sets the scope
    |
    v
It runs
    P8 (Gates on inputs, outputs, and approvals) enforces policy
    P9 (Trust earned before launch) requires passing the quality bar first
    P10 (Observability) provides evidence in production
    |
    v
Something changes — model, data, prompt, world
    P9 (Re-evaluation) detects the drift
    P11 (Lifecycle) triggers the right transition
    |
    v
It dies — planned or emergency
    P11 (Retirement or kill switch)
```

---

## RACI Matrix

**Roles:**
- **CAT** = Central AI Team
- **BO** = Business Owner (also Data Owner)
- **SEC** = Security / Governance

**RACI:** R = Responsible, A = Accountable, C = Consulted, I = Informed

| # | Principle | CAT | BO | SEC |
|---|---|---|---|---|
| P1 | Outcomes before agents | C | A, R | I |
| P2 | No anonymous agents | C | A, R | C, R |
| P3 | One front door, one control plane | A, R | C | C, R |
| P4 | Specs as code, or it didn't happen | A, R | R | C |
| P5 | Reuse before build | A, R | R | I |
| P6 | One vocabulary, enforced at launch | A, R | R | C |
| P7 | Least context, least privilege | A, R | C | A, R |
| P8 | Gates on inputs, outputs, and approvals | A, R | R | A, R |
| P9 | Trust is earned and re-earned | A, R | R | C, R |
| P10 | If you can't see it, you can't trust it | A, R | R | C, R |
| P11 | Lifecycle is mandatory | A, R | R | C, R |
| P12 | Pave the road, don't block it | A, R | C | C |

---

## Reference Architecture

```
                           +-----------------------+
                           |        Users          |
                           +-----------+-----------+
                                       |
                                       v
                      +----------------+----------------+
                      |           Front Door             |
                      |  Uber agent: intent routing,     |
                      |  discovery, session management   |
                      +----------------+----------------+
                                       |
                                       v
  +--------------------------------------------------------------------+
  |                        Control Plane                                |
  |                                                                    |
  |  +------------------+  +------------------+  +------------------+  |
  |  | Agent Runtime    |  | Identity &       |  | Agent Studio     |  |
  |  | Orchestration,   |  | Governance       |  | Build, register, |  |
  |  | tool execution,  |  | Agent identity,  |  | configure,       |  |
  |  | delegation,      |  | tags, policy,    |  | version, publish |  |
  |  | session mgmt     |  | RBAC, access     |  |                  |  |
  |  |                  |  | policies         |  |                  |  |
  |  +------------------+  +------------------+  +------------------+  |
  |                                                                    |
  |  +------------------+  +------------------+                        |
  |  | Observability    |  | Lifecycle        |                        |
  |  | Telemetry, usage,|  | Stage tracking,  |                        |
  |  | cost, provenance,|  | versioning,      |                        |
  |  | chain tracing    |  | kill switch      |                        |
  |  +------------------+  +------------------+                        |
  +--------+--------+--------+--------+--------+--------+-------------+
           |        |        |        |        |        |
           v        v        v        v        v        v
  +---------+-------+--------+--------+-----------+----------+
  | Context | Models| UI &   | Apps & | Specs as  | Developer|
  |         |       | Chat   | Integ- | Code      | Tooling  |
  |         |       |        | rations|           |          |
  +---------+-------+--------+--------+-----------+----------+
  |Semantic |LLMs,  |Chat,   |CRM,    |Git,       |Skills,   |
  |models,  |guard- |embed-  |ERP,    |CI/CD,     |templates,|
  |search,  |rails, |ded,    |collab  |version    |starter   |
  |ontol-   |eval-  |mobile  |tools   |control    |kits,     |
  |ogies    |uation |        |        |           |playbooks |
  +---------+-------+--------+--------+-----------+----------+

  P1 defines the outcome
  P2-P6 govern how agents are discovered, built, and registered
  P7-P10 govern how agents run and are observed
  P11 governs how agents transition and retire
  P12 is how you build it all — skills, templates, paved roads
```
