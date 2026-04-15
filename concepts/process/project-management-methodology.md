---
pageType: concept
id: concept.process.project-management-methodology
title: Project Management Methodology
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Project Management Methodology

> How we use the project management system — currently Vikunja at `/pm/`, potentially migrating to Plane. The PM system is one of our **three logs** — every material action has a record there, alongside the wiki (canonical knowledge) and handoffs (coordination threads).

## The three-log pattern

Every material action has a record in three places:

| Log | Purpose | Example |
|---|---|---|
| **Wiki** (canonical) | What was decided, what rules apply | Concept page with approved decision |
| **PM system** (timeline) | What was worked on, by whom, in what state | Task status transition log |
| **Handoffs** (`openclaw/handoffs/`) | Cross-persona coordination threads | Enforcer breakdown to `project-lead` |

Together: full audit + forward-looking visibility. Missing any one = gap.

## What gets logged in the PM system

Material actions — not chatter:

- **Task creation** (`project-lead` creates; others don't)
- **State transitions** (`pending → in_progress → in_review → testing → done`; plus `HOLD` and `archived`)
- **Enforcer approvals** at task kickoff (per [[concepts/process/task-kickoff-flow|task-kickoff-flow]] Step 6)
- **Cross-domain handoffs** (referenced by handoff file path so the thread is findable)
- **Wiki page creations / updates** triggered by the task (referenced by page ID or path)
- **Squad assemblies** ([[concepts/process/teams-and-squads|teams-and-squads]])
- **Persona lifecycle events** ([[concepts/process/persona-lifecycle|persona-lifecycle]])
- **QA cycles** — bug loop iterations per task
- **Haim approvals** (especially user-testable milestone hands-on acceptance)
- **Blockers** and their resolution
- **Escalations** (per the [[sources/coordination-rules|coordination-rules]] ladder)

## Task categories

Every task carries a category tag:

- **feature** — new capability shipping
- **research** — investigation, produces a wiki page + recommendation
- **design** — spec production, produces a [[concepts/meta/wiki-information-architecture|syntheses]] page
- **implementation** — code written, produces a PR
- **QA** — test scope framing (`qa-manager`) or test execution (`qa-tester`)
- **handoff** — cross-persona coordination work, produces a handoff file + wiki update
- **lifecycle** — persona add/remove/adapt per [[concepts/process/persona-lifecycle|persona-lifecycle]]
- **wiki-curation** — `project-lead` librarian work; ingests, lint, drift resolution
- **milestone-review** — close-out + retro for a milestone

## State transitions

```
pending → in_progress → in_review → testing → done
                                                 ↑
           HOLD (any state can escalate to HOLD)  |
                                  ↓              |
                                Haim decides → resolution
```

- **pending** — `project-lead` has created the task, enforcers not yet engaged
- **in_progress** — enforcers aligned, coder/worker is doing the work
- **in_review** — submitted for review (enforcer + `project-lead` per [[concepts/meta/documentation-hygiene|documentation-hygiene]])
- **testing** — QA cycle active (`qa-manager` frames, `qa-tester` executes)
- **done** — closed; milestone-review retro records it
- **HOLD** — blocked, escalated per escalation ladder
- **archived** — superseded or cancelled; kept for history

Additional tag:
- **haim-sign-off-required** — for tasks whose done-state needs hands-on Haim acceptance (see [[concepts/process/milestone-workflow|milestone-workflow]] Step 9)

## Task template — required fields

Every task record has these fields:

- **Title** — short, canonical-name consistent
- **Category** — one of the categories above
- **Squad** — per [[concepts/process/teams-and-squads|teams-and-squads]]
- **Assignee** — primary persona owning the work
- **DOD** — definition of done, explicit
- **Priority** — P0 (trading-bug, money) / P1 (feature-blocking) / P2 (polish)
- **Linked wiki pages** — page IDs of canonical pages relevant to this task
- **Parent milestone** — which milestone this rolls up to (if any)
- **Linked handoffs** — paths to handoff files for the task's coordination

## Who updates what

Per [[sources/coordination-rules|coordination-rules]] and GOVERNANCE.md:

- **`project-lead` creates and removes tasks.** Only `project-lead`.
- **Other personas update status only** — moving their own task `in_progress → in_review → testing → done`.
- **Nobody edits DOD or scope** mid-flight without going through [[concepts/coding/scope-discipline|scope-discipline]] (for code) or a formal scope change handoff.
- **Squad field** is set at task creation by `project-lead`; rarely changed.

## Haim-testable milestones

Per [[concepts/process/milestone-workflow|milestone-workflow]] Step 9: milestones that produce visible user-facing change get the `haim-sign-off-required` tag. `project-lead` does NOT close these until Haim has hands-on tested the result and approved.

Examples of user-testable milestones:
- Any UI change visible in the Dashboard, Observatory, or Debug Browser
- Any trade execution path (even on paper) — Haim watches the actual trades flow
- Any feature affecting the operator's information view
- Any money-flow change

Non-testable-by-Haim milestones (`project-lead` closes after enforcer + QA sign-off, no Haim hands-on):
- Internal refactors with no visible change
- Documentation updates
- Test coverage expansions
- Logging schema changes (unless they affect UI display)

## Tool-agnostic principle

The methodology above is the constant. The tool is currently Vikunja (`traderb.duckdns.org/pm/`); may migrate to Plane community edition later. When we migrate, the methodology carries over unchanged — only the UI changes.

Per [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]]: the canonical name for this log is "project management system" or "PM system" — not "Vikunja." Vikunja is today's implementation, not the concept.

## Historical lookup

Retrospective queries enabled by this methodology:
- "Which squad worked on task X?" — squad field lookup
- "What tasks did `python-dev` work on last sprint?" — assignee filter
- "Which tasks touched `entities/components/interactive-graph`?" — linked-wiki-page filter
- "How long did task-042 sit in `in_review`?" — state-transition timestamps
- "Which milestone close-outs needed Haim hands-on sign-off?" — tag filter

Squads + canonical task categories + linked wiki pages = three ways to slice the timeline.

## Interaction with other canonical pages

- [[concepts/process/teams-and-squads|teams-and-squads]] — squads logged here
- [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — task creation + state transitions aligned to this flow
- [[concepts/process/milestone-workflow|milestone-workflow]] — milestone-level task hierarchy
- [[concepts/meta/documentation-hygiene|documentation-hygiene]] — task submission goes through verification cascade
- [[sources/coordination-rules|coordination-rules]] — enforcer matrix used at state transitions
- [[concepts/process/persona-lifecycle|persona-lifecycle]] — lifecycle events are logged tasks

## What this is NOT

- Not a chat-replacement — the PM system doesn't track casual discussion. That belongs in handoffs or Telegram.
- Not a knowledge base — canonical knowledge lives in the wiki.
- Not a standup tool — daily-sync lives in `openclaw/daily-sync.md` for low-ceremony status.
