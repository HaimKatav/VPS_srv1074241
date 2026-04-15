---
pageType: concept
id: concept.meta.trading-numbers-discipline
title: Trading Numbers Discipline
status: active
approved-by-haim: true
domain: meta
updatedAt: 2026-04-15
---

# Trading Numbers Discipline

> Numbers that relate to trading or money are **sacred**. They get maximum care at every stage — research, planning, design, code, test, QA, human validation. Any stage catching a discrepancy halts forward motion and triggers investigation across all stages. Haim is the final judge.

This is a peer-principle to the "Parity is sacred" invariant in `/opt/traderb/openclaw/PROJECT_VISION.md` (outside wiki vault). Parity guarantees live matches replay; this page guarantees the numbers themselves are correct before they ever flow.

## What counts as a "trading number"

Anything that could affect money movement, risk exposure, or the trading decision itself:

- **Prices** — bid, ask, last, trade, signal-entry, stop, target
- **Quantities** — contract counts, position sizes
- **Math constants for instruments** — tick size, tick value, contract multiplier
- **Risk parameters** — stop distances, trailing stop deltas, max contracts, daily loss limits
- **Strategy parameters** — FVG gap minimums, supply/demand zone thresholds, trend filter lookbacks
- **Compliance numbers** — prop-firm rule thresholds (daily loss, drawdown, consistency %), exact-per-firm
- **Derived metrics** — P&L, drawdown, Sharpe, win rate, expectancy, consistency ratio
- **Timing thresholds** that affect trading decisions — session-start offsets, cooldown windows
- **Config values** captured per [[concepts/architecture/config-lineage|config-lineage]] — every number in config is subject to this discipline

Display-only decorative numbers (page view counts, UI animation timings) are NOT trading numbers — regular hygiene applies, not this discipline.

## The validation chain — every number passes every stage

Numbers flow from concept to live trade. Each stage has an insurance obligation:

### 1. Research stage — `strategy-architect` (strategist) or dedicated numbers owner

- Number is backed by sources — academic paper, internal research, prior-art references
- Derivation shown explicitly — "this threshold comes from X formula applied to Y data"
- Edge cases enumerated — what happens at the boundary, what happens at zero, what happens at max
- Units and ranges specified — is this in ticks? dollars? cents? basis points?
- **Output:** a research handoff or wiki page with the number + full provenance

**Per Haim's area-of-control rule** ([[concepts/meta/documentation-hygiene|documentation-hygiene]]): the number is owned by the role whose expertise covers it. If our current team has gaps in dedicated "numbers ownership," that gap is surfaced (see note on potential future numbers-persona role).

### 2. Design stage — architect + domain enforcer

- Spec calls out the number by canonical name — per [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]]
- Spec specifies: range, units, rounding behavior, edge-case handling
- Spec specifies where the number enters the system (config? hardcoded? derived at runtime?)
- Spec specifies boundary behavior — "if price crosses threshold mid-tick, round up vs down? which way?"
- **Output:** a synthesis page capturing the design

### 3. Code stage — `python-dev` (or `data-engineer`/`logging-specialist` for domain-specific code)

**The code itself must insure correctness.** Tests are a safety net, not the primary guarantee.

Required at the code level:
- **Unit-safe types** — don't store prices as `float`; use a `Price(ticks=...)` dataclass or equivalent. Mixing ticks and dollars at runtime must be a type error, not a silent bug.
- **Explicit validation at boundaries** — function receives a number? Assert the range before using it. Return a number? Assert it's in expected range before returning.
- **Explicit rounding rules** — every place that rounds states which way (round-up, round-down, banker's rounding, round-to-nearest-tick) with a comment linking to the spec.
- **No hidden unit conversions** — converting between ticks/dollars/contracts is always a named function (`dollars_to_ticks`, `contracts_to_risk_dollars`), never an inline `* 4` multiplier.
- **Assertions that cover the contract** — invariants of the function surface at test time AND at runtime (if feasible without perf impact).

### 4. Testing stage — `python-dev` writes tests, `qa-manager` frames coverage

Required coverage for trading numbers:
- **Unit tests for correctness** — input → expected output, covering the spec
- **Boundary tests** — at the min, at the max, at the exact threshold
- **Fuzz tests** — random inputs across the range, assert invariants hold
- **Regression tests** — historical replay, numbers produced match what was produced before (or are explicitly-approved different)
- **Parity tests** — live-capture vs replay match bit-for-bit (see `/opt/traderb/docs/COMPARISON_MODEL.md`)

`qa-manager`'s QA frame includes numerical coverage explicitly, per [[concepts/process/task-kickoff-flow|task-kickoff-flow]] Step 2.

### 5. QA execution — `qa-tester`

- Executes `qa-manager`'s frame
- Reports pass/fail per numerical sub-task
- Any numerical mismatch is a P0 finding — immediate escalation

### 6. Haim hands-on validation

After all prior stages pass, Haim tests the end-to-end flow with real data. This is **not a rubber stamp.** Haim:
- Exercises the flow through the UI
- Compares produced numbers against independent expectations (his knowledge, broker-side UI, reference data)
- Looks for anything that smells off

**If something's off at this stage** — investigate. Don't dismiss.

## When a discrepancy is detected at ANY stage

**STOP. Do not proceed.** Discrepancies in trading numbers are investigated, not patched.

### Investigation flow
1. Identify which stage(s) disagree with each other. (Example: research said X, code produces Y, QA didn't catch it.)
2. Identify the rightful owner of each number per the area-of-control rule.
3. Review QA test results + Dev test results side by side — did either catch this? Why not?
4. Check [[concepts/architecture/config-lineage|config-lineage]] — is the wrong number in the config of a live session?
5. Package the full case for Haim:
   - Which stage said what
   - Each owner's position + reasoning
   - QA / Dev test outcomes
   - Possible resolutions (keep X, keep Y, investigate further, halt the feature)
   - Cost of each resolution
6. Haim judges. Or asks more questions of the responsible agents.
7. Haim's resolution becomes new truth — ingested with `approved-by-haim: true`.

### What we never do
- **Never override a disagreement with a "let's go with X" unilateral call.** Numbers that touch money don't get unilateral calls.
- **Never mask a discrepancy with a try/except or a default value.** Discrepancy is a signal; masking removes the signal.
- **Never ship a number change without all stages re-validating.** Every stage that validated the old number re-validates the new one.

## Gap: dedicated numbers ownership

**Noted for post-Stage-1 review** (2026-04-15, Haim direction): if `strategy-architect` doesn't fully cover "numbers ownership" responsibility, we may need a dedicated persona for trading calculations / market math. Before adding, verify what `strategy-architect`'s scope covers vs doesn't — her IDENTITY/SOUL may need clarification first.

If we add this persona, go through [[concepts/process/persona-lifecycle|persona-lifecycle]] per usual — this is a non-trivial team change.

## Enforcement

- **Every number-touching task** triggers this discipline. `project-lead` identifies during [[concepts/process/task-kickoff-flow|task-kickoff-flow]] Step 1 whether a task is number-touching and tags it `trading-numbers` if so. That tag mandates:
  - Research stage output in the handoff
  - Design spec with range/units/rounding/edge-cases
  - Code review with type + validation + rounding checks
  - `qa-manager`'s QA frame explicit about numerical coverage
  - Haim hands-on sign-off before close
- **Per [[concepts/process/project-management-methodology|project-management-methodology]]**: `trading-numbers` tasks always also get `haim-sign-off-required`.
- **The enforcer for numbers** is whoever owns the domain — `strategy-architect` for strategy numbers, `compliance-officer` for compliance numbers, `risk-manager` for risk numbers, etc. The enforcer confirms the discipline was applied; `project-lead` confirms the enforcer did.

## Interaction with other canonical pages

- [[concepts/meta/documentation-hygiene|documentation-hygiene]] — the general hygiene chain; this page adds discipline on top for numbers
- [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]] — numbers have canonical names (stop_distance_ticks, not "stop" in one doc and "stopsize" in another)
- [[concepts/architecture/config-lineage|config-lineage]] — every number in config is traceable
- [[concepts/architecture/configurable-strategy-components|configurable-strategy-components]] — toggles + parameters are numbers subject to this discipline
- [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — enforcer steps where numbers get scrutinized
- [[concepts/process/milestone-workflow|milestone-workflow]] — Haim sign-off on user-testable milestones touches numbers
- [[sources/coordination-rules|coordination-rules]] — escalation ladder for disagreements
- `/opt/traderb/openclaw/PROJECT_VISION.md` §Parity is sacred — peer principle (outside wiki vault)

## What this is NOT

- Not paranoia — it's cost-of-error calibration. Wrong price = lost money = bad. The discipline exists to match the cost.
- Not a rule that slows work for its own sake — each stage's insurance is fast when done right.
- Not a replacement for parity discipline — both apply. Parity makes sure live matches replay; this makes sure the number was right in the first place.
