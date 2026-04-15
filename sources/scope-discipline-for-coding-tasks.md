---
pageType: source
id: source.scope-discipline-for-coding-tasks
title: Scope Discipline for Coding Tasks
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/coding/scope-discipline.md
ingestedAt: 2026-04-15T14:54:06.781Z
updatedAt: 2026-04-15T14:54:06.781Z
status: active
---

# Scope Discipline for Coding Tasks

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/coding/scope-discipline.md`
- Bytes: 5500
- Updated: 2026-04-15T14:54:06.781Z

## Content
````text
---
pageType: concept
id: concept.coding.scope-discipline
title: Scope Discipline for Coding Tasks
status: active
approved-by-haim: true
domain: coding
updatedAt: 2026-04-15
---

# Scope Discipline for Coding Tasks

> Governs what a coder does WHILE coding. For how tasks get defined before coding, see [[concepts/process/task-kickoff-flow|task-kickoff-flow]].

## Rule

By the time a coder starts writing code, the tasks have already been defined via the task-kickoff flow, approved by the relevant enforcers, AND the git environment has been prepared per the CI/CD readiness step (Step 6.5 of [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — Eitan gates trading-touching work; coders self-serve per standing policy otherwise). From that moment:

1. **The touch-area map is authoritative.** The map was declared in the task's DOD and approved by the enforcers. The coder codes inside it.
2. **Only edit what you declared you would edit.**
3. **Any unmapped change is a red flag.**
   - If a necessary change surfaces outside the planned scope mid-implementation, STOP coding.
   - Document what was found, why the change is necessary, what it impacts.
   - Send a handoff to the relevant enforcer(s). The change is approved ONLY with enforcer sanction + a wiki-trackable decision.

## Why

Scope creep during coding is the top source of:
- **Unexpected regressions** — you changed something no test was watching.
- **Parity drift** — you modified a code path that affects both live and backtest.
- **Hidden architectural debt** — you "just quickly fixed" a neighboring file instead of raising it as a separate task.
- **Security incidents** — you introduced a data-exposure path no one reviewed.
- **Compliance violations** — you tweaked broker-touching logic without Shira's sign-off.
- **UI incoherence** — you shipped code whose visual result doesn't match Rotem's approved design.

On a parity-critical trading bot, each of these is a money or credibility risk.

## How to produce a touch-area map

The map is a short list declared as part of the task brief:

```
Task: Implement Tradovate bracket-order adapter method

Files to touch:
- brokers/tradovate_adapter.py         (add method + tests wiring)
- brokers/tests/test_tradovate.py      (new contract tests)
- docs/BROKER_INTERFACE.md             (document method semantics)

Files explicitly NOT touched:
- trading_bot.py                       (no strategy-level change expected)
- store/                               (no data-model change)
- risk/                                (no risk-rule change)

Tests to add: 4 contract tests, 1 integration smoke
```

If the map grows long (>10 files) or crosses more than 3 domains, the task is too big — decompose it via the task-kickoff flow before starting.

## The red-flag procedure (mid-coding unplanned change)

When mid-coding the coder discovers something outside the map that needs to change:

1. **STOP** — do not make the unplanned change speculatively.
2. **Document** the find in the task thread: what was found, why it needs to change, what it impacts, what the coder believes the right fix is.
3. **Handoff** to the relevant enforcer (the one whose domain is affected by the discovered change). Use `openclaw/handoffs/<coder>-to-<enforcer>_<date>_<task-slug>_unplanned.md`.
4. **Up to 3 tries** to resolve:
   - Enforcer sanctions the change → coder proceeds, updates the task's touch-area map to reflect the new scope
   - Enforcer rejects → the change is NOT made; the original scope is delivered; a follow-up task is raised
   - Enforcer unsure → escalate per [[sources/coordination-rules|coordination-rules]] ladder
5. **Never** merge code that contains unplanned changes without a wiki-trackable sanction.

## Enforcement at code-review time

- Code reviewer (the same enforcer who pre-approved the task, per task-kickoff flow) checks the delivered diff against the declared touch areas AND the approved implementation approach.
- **Unplanned changes** in the diff → require explicit justification referencing the sanction handoff. Unjustified → reject.
- **Parity-relevant unplanned changes** automatically fail QA and require a debug-meeting per [[sources/coordination-rules|coordination-rules]] QA bug loop.

## Applies to

- Implementer personas: Lior (python-dev), Maya (frontend-dev), Dan (data-engineer when writing pipeline code), Gal (when writing instrumentation code).
- The enforcers named in the task-kickoff flow remain the review authority at code-review time.

## Interaction with other canonical pages

- [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — defines how the approved task arrives at the coder. Scope discipline takes over from that moment.
- [[concepts/process/milestone-workflow|milestone-workflow]] — the wider context (milestone phases).
- [[sources/coordination-rules|coordination-rules]] — escalation ladder used when enforcer sanction is unclear.

## What this is NOT

- Not a prohibition on refactoring. Refactoring is a first-class task — raise it as its own task, go through the task-kickoff flow.
- Not a prohibition on finding better approaches mid-implementation. If a better approach needs a wider scope, stop → handoff → sanction → proceed. The rule is explicit sanction, not "never change your mind."
- Not a veto system at code-review time. If an enforcer flags an unplanned change, the coder and enforcer align on the resolution; if they can't align, Ari escalates.

````

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
