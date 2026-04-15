---
pageType: source
id: source.teams-and-squads
title: teams and squads
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/process/teams-and-squads.md
ingestedAt: 2026-04-15T22:44:06.631Z
updatedAt: 2026-04-15T22:44:06.631Z
status: active
---

# teams and squads

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/process/teams-and-squads.md`
- Bytes: 6802
- Updated: 2026-04-15T22:44:06.631Z

## Content
````text
---
pageType: concept
id: concept.process.teams-and-squads
title: Teams and Squads
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Teams and Squads

> Personas are permanent roles. Squads are temporary teams assembled for specific tasks. This page defines the distinction, the pre-defined squad templates, the assembly mechanics, and the logging format.

## Persona vs squad

| Dimension | Persona | Squad |
|---|---|---|
| Nature | Permanent role (identity) | Temporary team (configuration) |
| Count | Currently 16 (main + 15 specialists) | Many squads per sprint, born + dissolved on demand |
| Owns | A role + its SOUL + AGENTS | A task (or task-line) |
| Lifecycle | Managed by [[concepts/process/persona-lifecycle|persona-lifecycle]] | Assembled for a task; dissolves when the task closes |
| Logged in | [[entities/team/index|team roster]], [[sources/coordination-rules|coordination-rules]] matrix | Per-task squad field in the PM system (see [[concepts/process/project-management-methodology|project-management-methodology]]) |

A persona can be in many squads simultaneously. A squad is just "who is working on task X together."

## Why squads

Right now work routes through the enforcer matrix — each task has its enforcers, but we don't have a named grouping for the people working together on a specific task-line. Introducing "squad" gives us:

1. **Visibility** — "who is working on the Tradovate integration?" answered via squad lookup, not ad-hoc memory.
2. **Parallel-work coordination** — when two agents work the same task-line from different angles (e.g., `python-dev` implementing + `data-engineer` validating data flow), a named squad captures that they're a unit.
3. **Logging for retrospective** — "which squad built strategy V3?" answerable from the PM log.
4. **Forward-looking capacity planning** — patterns of squad composition surface — "broker-integration work always engages the same 7 personas" — informs templates.

## Pre-defined squad templates

Common task types map to standard squad compositions. `project-lead` applies these automatically; deviations are explicit.

| Task type | Squad | Rationale |
|---|---|---|
| **Broker integration** (new adapter, broker API change) | `python-dev` + `data-engineer` + `security-specialist` + `compliance-officer` + `software-architect` + `qa-manager` + `logging-specialist` | Implementation + data + security + compliance + architecture + QA frame + logging |
| **Strategy component change** (new FVG variant, mean-reversion tweak) | `strategy-architect` + `python-dev` + `risk-manager` + `qa-manager` + `logging-specialist` + `software-architect` | Strategy owner + implementation + risk + QA + logging + architecture |
| **UI refresh / new panel** | `frontend-dev` + `ux-designer` + `transparency-agent` + `qa-manager` + `software-architect` | Frontend impl + UX design + explainability + QA + architecture |
| **Data pipeline change** | `data-engineer` + `python-dev` + `software-architect` + `qa-manager` + `logging-specialist` | Data owner + implementation + architecture + QA + logging |
| **Infrastructure / deployment** | `devops` + `security-specialist` + `software-architect` | DevOps + security + architecture |
| **Observability / logging work** | `logging-specialist` + `transparency-agent` + `frontend-dev` + `data-engineer` + `qa-manager` | Logging owner + explainability + frontend (for displays) + data + QA |
| **Compliance / prop-firm rule implementation** | `compliance-officer` + `risk-manager` + `python-dev` + `qa-manager` + `logging-specialist` | Compliance owner + risk + implementation + QA + logging |
| **Meta / process / governance** | `project-lead` + relevant-domain-enforcer(s) | `project-lead` always + whoever the process touches |
| **Persona lifecycle** | `project-lead` + Haim + potentially the persona involved | Per [[concepts/process/persona-lifecycle|persona-lifecycle]] |

Not exhaustive — new task categories generate new templates as patterns emerge.

## Ad-hoc squad assembly

When a task doesn't match a template, `project-lead` assembles a bespoke squad during [[concepts/process/task-kickoff-flow|task-kickoff-flow]] Step 1. The enforcer matrix from [[sources/coordination-rules|coordination-rules]] is the starting point — anyone touched by the task is in the squad.

## Logging format

Every task in the PM system carries a `squad` field. Format:

```
squad: broker-integration | [lior, dan, tamar, shira, idan, guy, gal]
```

Or for ad-hoc:

```
squad: ad-hoc-<slug> | [<agent-ids>]
```

Per [[concepts/process/project-management-methodology|project-management-methodology]]: squads are logged in the PM system (Vikunja / future Plane), queryable by squad ID to see all tasks the squad touched.

## Multi-angle work on the same task-line

The forward-looking use case: two agents work different angles on the same task.

**Example:** implementing a new broker adapter.
- Squad: `broker-integration`
- `python-dev` writes the adapter code
- `data-engineer` parallel-validates data flow (ensuring the adapter emits what the pipeline expects)
- They're in the same squad, logged as coordinating, but their individual tasks are separate records (per the PM methodology)

The squad designation is what makes this legible retrospectively — "oh, `python-dev` and `data-engineer` were in `broker-integration` on task-047" — without the squad tag, the relationship is ad-hoc memory.

## Rules

- **`project-lead` assembles + dissolves** squads. Other personas don't self-assemble.
- **Every task with 3+ personas touched** gets an explicit squad tag (auto-applied from template or named ad-hoc).
- **Solo tasks or 2-persona tasks** don't need a squad tag — the enforcer matrix is sufficient.
- **Squad tags are append-only history** — once applied to a task, the squad tag stays in the log forever. Renaming squads creates a new template; old tasks still show their original squad.

## Interaction with other canonical pages

- [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — squad assembly happens at Step 1 distribution
- [[concepts/process/project-management-methodology|project-management-methodology]] — how squads are logged in the PM system
- [[sources/coordination-rules|coordination-rules]] — enforcer matrix is the starting point for squad composition
- [[entities/team/index|team roster]] — the personas available for squad assembly

## What this is NOT

- Not a hierarchy on top of personas — squads don't have "leaders." `project-lead` coordinates; the enforcer matrix routes.
- Not a replacement for enforcer relationships — squads are about coordination, enforcers are about approval.
- Not a bureaucracy — squads emerge from actual work. If naming a squad adds no value, skip it.

````

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
