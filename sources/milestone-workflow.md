---
pageType: source
id: source.milestone-workflow
title: milestone workflow
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/process/milestone-workflow.md
ingestedAt: 2026-04-15T22:48:04.181Z
updatedAt: 2026-04-15T22:48:04.181Z
status: active
---

# milestone workflow

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/process/milestone-workflow.md`
- Bytes: 7238
- Updated: 2026-04-15T22:48:04.181Z

## Content
```text
---
pageType: concept
id: concept.process.milestone-workflow
title: Milestone Workflow
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Milestone Workflow

> **Mandatory for any non-trivial milestone.** Research and design complete BEFORE implementation starts. No code exists without an approved spec.

## Purpose

This page defines how a milestone moves from "Haim says go" to "milestone closed." It extends GOVERNANCE.mds CEO Directive Intake and layers concrete phase gates on top. It works alongside the escalation ladder and enforcer matrix defined in _shared/coordination-rules.md — this page is the process; that page is the coordination mechanics.

## The nine steps

### 1. INTAKE
Haim defines the milestone in plain language. Output: one sentence of intent + rough success criteria. Intake happens in whichever channel Haim uses (Telegram, chat, meeting note).

### 2. DECOMPOSITION
`project-lead` translates the milestone into phase buckets: research / design / implementation / QA. Produces a crew manifest (which personas, what role). Posts the plan to Haim for approval.

**Gate:** no work starts until Haim approves the plan.

### 3. RESEARCH PHASE
Domain specialists — typically `strategy-architect` (strategy), `data-engineer` (data/pipeline), `software-architect` (software architecture), `security-specialist` (security), `risk-manager` (risk), `compliance-officer` (compliance) — produce research notes, ADRs, and recommended approaches for anything thats not already decided.

- Output: handoff files to `project-lead` with structured findings + recommendations.
- `project-lead` ingests to concepts/<domain>/ (for principle-level findings) or syntheses/<domain>/ (for situation-specific recommendations) with status proposed.
- Provenance: inferred until Haim reviews.

**Gate:** research outputs reviewed by Haim. Approved findings flip to approved-by-haim: true.

### 4. DESIGN PHASE
Architects and designers produce detailed specs based on approved research.

- `software-architect` — software architecture (module splits, abstractions, ADRs)
- `strategy-architect` — strategy logic spec
- `ux-designer` — UX spec
- `data-engineer` — data pipeline spec
- All output lands as syntheses/<domain>/<spec-name>.md with status proposed.

Specs must include: scope (whats in, whats NOT), DOD, dependencies, tests to add, risks, rollback plan.

### 5. SPEC GATE
Haim reviews the bundle of specs for the milestone.

- Approved specs get approved-by-haim: true.
- **No spec approval → no code.** Non-negotiable.
- Disputed specs go back to research with explicit questions.

### 6. IMPLEMENTATION PHASE
Builders (`python-dev`, `frontend-dev`, `data-engineer`, `logging-specialist`) receive the approved specs and enter the **task-kickoff flow** (see concepts/process/task-kickoff-flow.md):

- `project-lead` distributes the spec to relevant enforcers; enforcers produce domain breakdowns; align (escalate to Haim on conflict).
- `project-lead` collates a unified brief; coder creates concrete tasks with DOD and touch-area maps; enforcers approve the task list (up to 3 revise rounds).
- **Step 6.5 CI/CD preparation** (`devops`): git environment prepared. Gate model for trading-touching code; coder self-serve per standing policy otherwise. No coding until git is ready.
- Only then coding begins. **Scope discipline** (concepts/coding/scope-discipline.md) applies from that moment.
- Code review happens continuously under the enforcer matrix (see _shared/coordination-rules.md).

### 7. QA CYCLE
**`qa-manager`** (qa-manager) defined the QA frame in step 6 (task-kickoff-flow step 2). **`qa-tester`** executes per `qa-manager`'s frame, creating her own concrete testing sub-tasks for each item in the frame, and tests against the approved spec's DOD. Bug loop per coordination-rules §QA bug loop — up to 3 fix-retest cycles before `qa-manager` escalates to a debug meeting.

**Gate:** QA passes OR a debug-meeting decision is produced (re-scope / re-implement / re-design).

### 8. ENFORCER SIGN-OFF
Relevant enforcers confirm their domain is clean before `project-lead` closes:
- `software-architect` — architecture clean, no debt introduced
- `risk-manager` / `compliance-officer` — if trading logic / prop rules touched
- `security-specialist` — if auth / secrets touched
- `data-engineer` — if data pipeline touched
- `ux-designer` — if UI changed
- `devops` — CI green, deployment ready

### 9. MILESTONE CLOSE-OUT
`project-lead` compiles the close-out summary: what shipped, what didn't, what was learned.
- Writes a synthesis page to `syntheses/milestones/<milestone-id>.md`.
- **If the milestone is user-testable** (per [[concepts/process/project-management-methodology|project-management-methodology]] §Haim-testable milestones): Haim performs hands-on testing — not a rubber stamp, actual exercise of the surface with real data. `project-lead` does NOT close without this.
- **If the milestone touches trading numbers** (per [[concepts/meta/trading-numbers-discipline|trading-numbers-discipline]]): Haim's hands-on validation includes checking produced numbers against independent expectations.
- Close-out meeting record saved to `openclaw/meetings/`.
- Retro: token usage, rework rate, stall patterns — feed into next sprint.
- All associated tasks in the PM system transition to `done`; squad history archived.

## Phase gates summary

| Gate | Who approves | What happens if blocked |
|---|---|---|
| After Decomposition (step 2) | Haim | Crew manifest revised |
| After Research (step 3) | Haim | Research re-scoped or extended |
| After Design (step 5) | Haim | Design returned with questions |
| After QA (step 7) | `qa-tester` + `project-lead` | Debug meeting, decision page |
| After Enforcer sign-off (step 8) | Enforcers | Rework per enforcers direction |
| Milestone close (step 9) | Haim | Milestone reopened |

## Interaction with other canonical pages

- [[sources/coordination-rules|coordination-rules]] — escalation ladder, enforcer matrix, QA bug loop. This page uses those mechanics; does not redefine them.
- [[concepts/coding/scope-discipline|scope-discipline]] — referenced in step 6 for the touch-area mapping rule. Canonical for coding scope.
- [[concepts/meta/wiki-information-architecture|wiki-information-architecture]] — governs where research/spec pages land (concepts vs syntheses vs entities).
- /opt/traderb/openclaw/GOVERNANCE.md — higher-level governance (DOD rules, review chain, persona roles). This page is the procedural spine; GOVERNANCE.md is the policy layer.

## Early-cycle caveat

For the first few milestones, run this workflow at the smallest possible scope. Pick a narrow task. Validate each persona executes their role before scaling milestone size. The goal of early cycles is to validate team mechanics, not ship volume. See _shared/coordination-rules.md Early-cycle caveat.

## What this prevents

- Implementation that drifts from the plan (scope creep)
- Undocumented architectural decisions (debt without trace)
- Builders guessing about intent (parity risk)
- Unapproved trading rules in production (money risk)
- Milestones closing without retro feedback (we stop learning)

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
