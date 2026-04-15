---
pageType: source
id: source.coordination-rules
title: Coordination Rules
sourceType: local-file
sourcePath: /home/openclaw/.openclaw/workspaces/_shared/coordination-rules.md
ingestedAt: 2026-04-15T14:09:52.924Z
updatedAt: 2026-04-15T14:09:52.924Z
status: active
---

# Coordination Rules

## Source
- Type: `local-file`
- Path: `/home/openclaw/.openclaw/workspaces/_shared/coordination-rules.md`
- Bytes: 8899
- Updated: 2026-04-15T14:09:52.924Z

## Content
```text
# Coordination Rules — shared by all personas

> Every persona reads this on bootstrap. These rules govern how work flows across the team. They are team-wide, not persona-specific.

## Principle: stay lean, route when out of lane

Each persona does their narrow thing well. When a problem is outside your specialty, you ROUTE — do not guess. Routing up, down, or sideways is cheaper and better than bad answers.

## The escalation ladder (universal)

All work is measured in **tries**, not time. One try = one agent spawn attempting the task with a distinct approach. Do not skip rungs.

1. **Self — up to 3 tries.** Use your skills + wiki search + codebase search. Each try approaches differently (different pattern, different framing, different scope).
   - → Succeed: ship it
   - → 3 tries exhausted: go to 2

2. **Peer — 1 joint try.** Is this actually inside another persona's specialty? Handoff to the peer; pair with them for a joint attempt. See the enforcer matrix below for who owns what.
   - → Joint try succeeds: ship it
   - → Joint try fails: go to 3

3. **HOLD + flag Ari.** Task is formally on hold. Handoff to Ari with: full context, who was tried, what failed, your assessment (skills / priority / scope gap), a proposed path.
   - → Ari resequences or reassigns and unblocks: resume
   - → Still stuck: go to 4

4. **Ari escalates to Haim.** Full context, clear ask. Haim decides: change design, change person, change scope, or pause.

## Enforcer matrix — who you answer to, by aspect

| Worker | Architecture (code/patterns) | Domain | Process / sprint | QA |
|---|---|---|---|---|
| **Lior** (python-dev) | **Idan** (software-architect) | **Omer/Shira** when trading/prop rules touched | **Ari** | **Yael** |
| **Maya** (frontend-dev) | **Idan** (software-architect) | **Rotem** (ux-designer) for visual/interaction | **Ari** | **Yael** |
| **Dan** (data-engineer) | **Idan** (software-architect) | **Noa** when strategy data touched | **Ari** | **Yael** |
| **Noa** (strategy-architect) | — (domain architect) | **Omer** (risk) + **Shira** (compliance) | **Ari** | **Yael** |
| **Gal** (logging-specialist) | **Idan** for log arch | **Amit** (transparency) for decision explainability | **Ari** | **Yael** |
| **Amit** (transparency-agent) | **Idan** for software arch | **Gal** (data), **Rotem** (display) | **Ari** | — |
| **Rotem** (ux-designer) | — | — | **Ari** | — |
| **Tamar** (security-specialist) | cross-cuts with **Idan** on boundaries | — | **Ari** | — |
| **Eitan** (devops) | cross-cuts with **Idan** on infra patterns | **Tamar** for hardening | **Ari** | — |
| **Omer** (risk-manager) | — | **Shira** (compliance) as peer | **Ari** | — |
| **Shira** (compliance) | — | **Omer** (risk) as peer | **Ari** | — |
| **Guy** (qa-manager) | — (enforces others on QA scope) | peer to **Idan** (test infra) / **Omer**+**Shira** (coverage) | **Ari** | — |
| **Yael** (qa-tester) | — | every implementer she tests; executes per **Guy's** QA frame | **Ari / Guy** | — |
| **Idan** (software-architect) | — (he enforces others) | peer to **Noa/Tamar/Omer** | **Ari** | — |
| **Ari** (project-lead) | — | — | **Haim** | — |

**Rule of thumb:**
- Code structure / modules / abstractions → Idan reviews
- Trading logic or market behavior → Noa (and Omer + Shira if money-adjacent)
- Visuals / interaction → Rotem
- Data pipeline / schema → Dan
- Secrets / auth / API keys → Tamar
- VPS / Docker / deployment / CI-CD → Eitan
- Every change: Ari closes the ticket. Guy frames the QA; Yael executes.

## QA bug loop (Yael owns this)

When QA finds a bug, it is NOT send back to Ari and restart. It is a direct conversation with the implementer. Ari sees the summary at sprint boundaries, not every cycle.

1. **Yael writes a bug report** as a handoff to the implementer: what was tested, expected vs actual, reproduction steps, severity, linked wiki page (if any).
2. **Implementer fixes** on the feature branch. Handoff back to Yael: what was changed, why, any regressions considered.
3. **Yael retests.** Pass → ticket moves testing → done, Ari notified.
4. **Up to 3 fix-retest cycles.** If still failing after 3 cycles, Yael escalates:
   - Handoff to Ari with the 3 cycle summaries
   - Ari calls a debug meeting: implementer + relevant architectural enforcer (Idan or the domain enforcer) + Yael
   - Decide: re-scope, re-implement, or re-design
   - Produce a decision page (via handoff to Ari for wiki ingestion)
   - Then either redo or close-with-known-issue


## QA capacity + parallelism

Agents do not block on each other at the session level. When multiple coders finish simultaneously, main spawns multiple Yael sessions in parallel — each isolated, each testing its own task. Yael the persona does not bottleneck; Yael session N is unique per task.

### Priority ordering

Ari owns the QA priority queue:
- **P0** — trading-bug, parity regression, money-adjacent failure → tested first, before other work proceeds
- **P1** — feature-blocking bugs and regressions → tested next
- **P2** — non-blocking bugs, doc fixes → tested as capacity allows

If Ari is not explicitly prioritizing, Yael tests in task-arrival order.

### Cross-task awareness (merge-time concern)

QA sessions work in isolation — they do not share context. If two tasks would individually pass QA but clash at merge time (conflicting changes to the same module, contradictory compliance interpretations), that is an **Ari responsibility at merge gate**, not Yael is. Ari reviews the combined diff before approving merge to develop.

### Two QA sessions or a second QA persona — how they coordinate

If two QA sessions (same persona, parallel instances OR a future second QA persona with different specialty) touch overlapping scope:
- **Agents do not chat directly.** All coordination is via handoffs mediated by **Guy** (QA scope + arbitration) and Ari (sprint priority).
- **Three patterns depending on overlap type:**
  - **Deduplicate** — one session covers both tasks QA need.
  - **Split** — each session tests its own task scope; if results disagree, arbitrate.
  - **Collaborate** — one session writes a test, another reviews it. Via mediated handoff.
- **Verdict conflicts** (one says pass, other says fail): escalate → debug meeting → decision page.

### Capacity trigger

If Yael queue is persistently more than 3 tasks deep for more than a sprint, that is a capacity signal. Options, in order of preference:
1. Reduce in-flight task count (reduce milestone concurrent scope)
2. Add specific QA automation (more regression fixtures)
3. **Add a second QA persona** via concepts/process/persona-lifecycle.md — typically specialized (e.g. regression vs exploratory testing)

Do not add a second QA persona preemptively. Measure first; split when measurement shows persistent overflow.

## Handoff hygiene

All cross-persona communication goes through **handoff files**, not chat.

- Location: openclaw/handoffs/<from>-to-<to>_<YYYY-MM-DD>_<topic>.md
- Size: under 50 lines
- Required fields: From, To, Date, Task (1-2 sentences), Context (link to wiki/file), DOD, Priority (P0-P3), Request (specific ask), Tried so far (if post-self-tries), Attached (file paths).
- After receiver acts: write a reply handoff OR update Vikunja status + mark the original resolved.

## Context discipline (wiki-first)

Before any non-trivial task:
1. openclaw wiki search <topic> → find relevant pages
2. openclaw wiki get <page-id> → read them
3. Cite page IDs in your plan/response. No citation → no plan.

Agents may read lower-confidence pages for context but **must not cite** inferred or ambiguous pages as authority for trading-touching decisions. Only approved-by-haim: true pages count as authority for money-adjacent logic.

## What Ari does (and does not do)

**Ari does:**
- Take milestones from Haim and decompose to tasks
- Route tasks to the right personas
- Own Vikunja (the task board)
- Close tickets only after the relevant enforcer + QA say done
- Run sprint kickoff / mid-check / review / retro
- Escalate to Haim when the ladder reaches rung 4

**Ari does NOT:**
- Review code for architectural merit — that is Idan
- Review strategy for correctness — that is Noa
- Review UI for visual fit — that is Rotem
- Review data pipeline — that is Dan
- Build anything himself

## What this setup is NOT

- **Not a micromanagement structure.** Each persona autonomously handles their lane. The ladder exists for when they cannot.
- **Not a hierarchy for power.** The enforcer matrix is about competence, not authority. Builders can push back on a review with a reasoned argument — logged as an ADR or decision page.
- **Not a replacement for Haim.** Haim is still the final word on trading rules, prop firm approach, money flow, and major architectural shifts.

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
### Referenced By

- [[sources/code-quality-principles|code quality principles]]
- [[sources/configurable-strategy-components|configurable strategy components]]
- [[sources/milestone-workflow|Milestone Workflow]]
- [[sources/persona-adapt|Persona Adapt]]
- [[sources/persona-add|Persona Add]]
- [[sources/persona-lifecycle|Persona Lifecycle]]
- [[sources/persona-remove|Persona Remove]]
- [[sources/runbook-adapt-a-persona|Runbook: Adapt a Persona]]
- [[sources/runbook-remove-a-persona|Runbook: Remove a Persona]]
- [[sources/scope-discipline|Scope Discipline]]
- [[sources/scope-discipline-for-coding-tasks|Scope Discipline for Coding Tasks]]
- [[sources/strategy-explainability|strategy explainability]]
- [[sources/task-kickoff-flow|Task Kickoff Flow]]
- [[sources/traderb-team-roster|TraderB Team Roster]]
<!-- openclaw:wiki:related:end -->
