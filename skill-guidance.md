## Plan to Build a Skill When Almost All of These Are True

### Skill Creation Criteria (Target: 7+/10)

| # | Criterion | Description |
|---|-----------|-------------|
| ☐ | **Recurring** | The task comes up repeatedly across users/sessions |
| ☐ | **Multi-Step** | Requires 3+ coordinated actions to complete |
| ☐ | **Domain Expert** | Doing it well requires knowledge most users lack |
| ☐ | **Error-Prone** | Users commonly make mistakes without guidance |
| ☐ | **Discoverable** | Users can describe what they want in natural language |
| ☐ | **Bounded** | Has a clear start, decision points, and end state |
| ☐ | **Tool Orchestration** | Involves coordinating multiple tools together |
| ☐ | **Guardrails Add Value** | There are pitfalls, gotchas, or best practices that should be enforced |
| ☐ | **Context-Dependent** | Behavior should adapt based on what's discovered |
| ☐ | **Teachable Moment** | User benefits from learning while the task runs |

### Quick Reference

| Score | Recommendation |
|-------|----------------|
| 9-10 | Strong skill candidate - prioritize building |
| 7-8 | Good skill candidate - build if necessity proven / Refer to domain/technical experts |
| 5-6 | Consider a lightweight template or workflow doc instead |
| <5 | Probably not a skill - use existing tools or one-off task |

## How do you manage sprawl of skills?

Keep skill sprawl under control by treating skills like a product:
- Design them intentionally.
- Prune aggressively
- Centralize ownership

### Managing Skill Sprawl:

1. **Start with a small, clear set**
   Limit yourself to a handful of top-level skills aligned to real domains (e.g., "Code review", "Docs", "Data analysis"), not every micro-task.
   Resist creating a new skill until you've seen a pattern repeat several times and can articulate when it should be used and when it shouldn't.

2. **Use strict naming and structure**
   Adopt naming conventions like domain-purpose (e.g., frontend-code-review, marketing-landing-pages) and document them in one shared place.
   Keep a consistent internal layout: SKILL.md, sub-skills/..., references/..., scripts/... so every skill feels familiar and easy to navigate.

3. **Prefer sub-skills inside a skill over new skills**
   When you feel tempted to create "yet another skill" for a narrow case, make it a sub-skill in an existing domain skill instead (e.g., a new review checklist sub-skill under code-review).
   Reserve new skills for genuinely different domains or audiences, not for each variation of the same process.

4. **Keep skills concise and focused**
   Cap SKILL.md size and avoid long, nested references; link to reference files instead of putting everything in one blob.
   Remove "encyclopedic" content the model already knows and keep only org-specific rules, workflows, and examples.

5. **Establish an owner and review cycle**
   Assign an owner per skill (person or team) responsible for updates, deprecations, and answering "should we add this here or somewhere else?".
   Run periodic "skill audits" (monthly or quarterly): merge overlapping skills, delete unused ones, and tighten descriptions so auto-selection behaves predictably.

6. **Make discovery intentional**
   Maintain a short "skill catalog" listing skills, what they do, and example prompts, so people reuse instead of inventing new ones.
   Write precise names/descriptions so the right skill is triggered; vague descriptions cause both underuse and accidental over-triggering.

> **Skill Catalog (coming soon):** Cortex Code is introducing a Skill Catalog that allows you to share and publish skills with colleagues and teams across a Snowflake account. 

## How to Manage Skills

For CoCo-specific skill management (organizing, documenting, versioning, sharing, and maintaining skills), use the built-in tooling:

- **List skills:** `/skill list` or `cortex skill list`
- **Add skills:** `cortex skill add <path|git-url|stage>`
- **Share skills:** `cortex skill publish --to-stage @STAGE` or via Git repository
- **Structure guidance:** Invoke `$skill-development` for the bundled best practices

> Invoke `$skill-development` in a CoCo session for detailed guidance on skill file structure, frontmatter, workflow design, tool definitions, scripts, modularity decisions, and a quick reference checklist.

## Skill Release Checklist

> Invoke `$skill-development` in a CoCo session for a comprehensive release checklist covering conciseness, frontmatter, workflow, tools/scripts, and structure validation.

## Quality Principles

### The CRAFT Framework

| Principle | Description |
|-----------|-------------|
| **C**lear | Single responsibility, obvious purpose |
| **R**esilient | Handles errors gracefully, recovers when possible |
| **A**daptive | Adjusts to context, doesn't assume |
| **F**ocused | Does one job well, delegates the rest |
| **T**eaching | User learns something while skill runs |

### Good Skill vs Anti-Patterns

| Good Skill | Anti-Patterns |
|------------|---------------|
| Single responsibility (one job-to-be-done) | **KITCHEN SINK:** Does too many things -> Split into focused skills |
| Clear entry/exit points | **HARDCODED ASSUMPTIONS:** Works only for author's setup -> Make it adaptable |
| Fails gracefully with actionable guidance | **SILENT FAILURES:** Proceeds without validating -> Add explicit checks |
| Asks clarifying questions vs assuming | **NO ESCAPE HATCH:** User can't override/customize -> Provide options |
| Progressive disclosure (simple -> advanced) | **TOOL DUPLICATION:** Duplicates core tool functionality -> Use existing tools |
| Teaches while doing (user learns) | **INFINITE LOOPS:** No termination condition -> Define clear exit criteria |
| Works across different environments/setups | **OVER-QUESTIONING:** Asks too many questions -> Discover what you can |
| Provides escape hatches for advanced users | **MAGIC BEHAVIOR:** Does things without explaining -> Be transparent |
