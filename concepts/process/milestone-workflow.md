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
Ari (project-lead) translates the milestone into phase buckets: research / design / implementation / QA. Produces a crew manifest (which personas, what role). Posts the plan to Haim for approval.

**Gate:** no work starts until Haim approves the plan.

### 3. RESEARCH PHASE
Domain specialists — typically Noa (strategy), Dan (data/pipeline), Idan (software architecture), Tamar (security), Omer (risk), Shira (compliance) — produce research notes, ADRs, and recommended approaches for anything thats not already decided.

- Output: handoff files to Ari with structured findings + recommendations.
- Ari ingests to concepts/<domain>/ (for principle-level findings) or syntheses/<domain>/ (for situation-specific recommendations) with status proposed.
- Provenance: inferred until Haim reviews.

**Gate:** research outputs reviewed by Haim. Approved findings flip to approved-by-haim: true.

### 4. DESIGN PHASE
Architects and designers produce detailed specs based on approved research.

- Idan — software architecture (module splits, abstractions, ADRs)
- Noa — strategy logic spec
- Rotem — UX spec
- Dan — data pipeline spec
- All output lands as syntheses/<domain>/<spec-name>.md with status proposed.

Specs must include: scope (whats in, whats NOT), DOD, dependencies, tests to add, risks, rollback plan.

### 5. SPEC GATE
Haim reviews the bundle of specs for the milestone.

- Approved specs get approved-by-haim: true.
- **No spec approval → no code.** Non-negotiable.
- Disputed specs go back to research with explicit questions.

### 6. IMPLEMENTATION PHASE
Builders (Lior, Maya, Dan, Gal) receive the approved specs and enter the **task-kickoff flow** (see concepts/process/task-kickoff-flow.md):

- Ari distributes the spec to relevant enforcers; enforcers produce domain breakdowns; align (escalate to Haim on conflict).
- Ari collates a unified brief; coder creates concrete tasks with DOD and touch-area maps; enforcers approve the task list (up to 3 revise rounds).
- **Step 6.5 CI/CD preparation** (Eitan): git environment prepared. Gate model for trading-touching code; coder self-serve per standing policy otherwise. No coding until git is ready.
- Only then coding begins. **Scope discipline** (concepts/coding/scope-discipline.md) applies from that moment.
- Code review happens continuously under the enforcer matrix (see _shared/coordination-rules.md).

### 7. QA CYCLE
Yael tests against the approved specs DOD. Bug loop per coordination-rules QA bug loop — up to 3 fix-retest cycles before escalation to a debug meeting.

**Gate:** QA passes OR a debug-meeting decision is produced (re-scope / re-implement / re-design).

### 8. ENFORCER SIGN-OFF
Relevant enforcers confirm their domain is clean before Ari closes:
- Idan — architecture clean, no debt introduced
- Omer / Shira — if trading logic / prop rules touched
- Tamar — if auth / secrets touched
- Dan — if data pipeline touched
- Rotem — if UI changed
- Eitan — CI green, deployment ready

### 9. MILESTONE CLOSE-OUT
Ari compiles the close-out summary: what shipped, what didnt, what was learned.
- Writes a synthesis page to syntheses/milestones/<milestone-id>.md.
- Haim reviews and accepts.
- Close-out meeting record saved to openclaw/meetings/.
- Retro: token usage, rework rate, stall patterns — feed into next sprint.

## Phase gates summary

| Gate | Who approves | What happens if blocked |
|---|---|---|
| After Decomposition (step 2) | Haim | Crew manifest revised |
| After Research (step 3) | Haim | Research re-scoped or extended |
| After Design (step 5) | Haim | Design returned with questions |
| After QA (step 7) | Yael + Ari | Debug meeting, decision page |
| After Enforcer sign-off (step 8) | Enforcers | Rework per enforcers direction |
| Milestone close (step 9) | Haim | Milestone reopened |

## Interaction with other canonical pages

- _shared/coordination-rules.md — escalation ladder, enforcer matrix, QA bug loop. This page uses those mechanics; does not redefine them.
- concepts/coding/scope-discipline.md — referenced in step 6 for the touch-area mapping rule. Canonical for coding scope.
- concepts/meta/wiki-information-architecture.md — governs where research/spec pages land (concepts vs syntheses vs entities).
- /opt/traderb/openclaw/GOVERNANCE.md — higher-level governance (DOD rules, review chain, persona roles). This page is the procedural spine; GOVERNANCE.md is the policy layer.

## Early-cycle caveat

For the first few milestones, run this workflow at the smallest possible scope. Pick a narrow task. Validate each persona executes their role before scaling milestone size. The goal of early cycles is to validate team mechanics, not ship volume. See _shared/coordination-rules.md Early-cycle caveat.

## What this prevents

- Implementation that drifts from the plan (scope creep)
- Undocumented architectural decisions (debt without trace)
- Builders guessing about intent (parity risk)
- Unapproved trading rules in production (money risk)
- Milestones closing without retro feedback (we stop learning)
