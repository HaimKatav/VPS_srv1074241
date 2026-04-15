## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
le
sourcePath: /opt/traderb-wiki/concepts/ux/strategy-explainability.md
ingestedAt: 2026-04-15T22:48:04.402Z
updatedAt: 2026-04-15T22:48:04.402Z
status: active
---

# strategy explainability

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/ux/strategy-explainability.md`
- Bytes: 6143
- Updated: 2026-04-15T22:48:04.402Z

## Content
```text
---
pageType: concept
id: concept.ux.strategy-explainability
title: Strategy Explainability in the UI
status: active
approved-by-haim: true
domain: ux
updatedAt: 2026-04-15
---

# Strategy Explainability in the UI

## The requirement

Every trade, when viewed in the UI, shows the complete decision trail: **why the bot entered, why it exited, which strategy components fired, which risk gates passed or blocked, which config was in force.** A trader must be able to open a trade and understand the decision in under a minute, without reading code.

This is non-negotiable. See the "Human oversight" principle in `PROJECT_VISION.md` — the bot is NOT a black box.

## What "explainable" means concretely

For any trade, the viewer sees:

### 1. Entry reasoning
- Which signal(s) fired (supply/demand zone, liquidity sweep, FVG, momentum etc.)
- Which confirmations passed (trend filter, session filter, volatility gate)
- Which filters did NOT reject (explicit, not implicit)
- Timestamp + price at the decision moment

### 2. Exit reasoning
- Which condition triggered the exit (target hit / stop hit / trailing hit / time stop / compliance rule)
- If trailing stop: the trailing price path
- If compliance-triggered: which rule (daily loss limit, no-overnight, max contracts)

### 3. Components that contributed
- Every strategy component evaluated during the decision
- Which returned "proceed" vs "reject"
- For the components that contributed: a short summary of their reasoning (one sentence each)
- This maps directly onto `concepts/architecture/configurable-strategy-components.md` — components must emit their reasoning, not just a boolean.

### 4. Config in force
- Link to the config snapshot that was active (per `concepts/architecture/config-lineage.md`)
- Clickable — lands on a rendered view of the config, with diffs against "previous" and "baseline"
- Specifically called out: which components were enabled/disabled via toggle

### 5. Time-aligned context
- Chart with markers at the key decision moments (entry, stop, target, trailing adjustments, exit)
- Indicators visible at decision time (not just current)
- Zoomable (see `concepts/architecture/reusable-tools.md` — the graph tool)

## Cross-cutting ownership

This requirement is touched by many personas — coordination matters.

- **`strategy-architect`** — ensures the signal engine emits decision steps as *structured data*, not just fires-and-forgets. Every strategy component exposes its reasoning.
- **`logging-specialist`** — logs every decision step with structure that is queryable AND renderable in the UI. Log schema owns this.
- **`transparency-agent`** — owns the principle of explainability; reviews whether each decision is actually understandable from the log, not just present. "Is this enough to understand the trade?" is `transparency-agent`'s lens.
- **`ux-designer`** — designs the trade-detail view so a trader can scan the decision trail quickly. Information hierarchy is her lane.
- **`frontend-dev`** — implements the view. HTML/CSS/JS.
- **`qa-manager`** — frames QA that includes: every trade logged has a complete reasoning trail; every trade's UI view renders all five sections above.

## Why this is non-negotiable

The bot will trade real money. Trading real money on decisions we cannot explain is a risk we do not accept. Every bad trade must be analyzable after the fact so we can improve. Every good trade must be understood so we can reinforce what works. "It worked" without "because X" is a lucky fluke we cannot learn from.

## Enforcement

- **`transparency-agent` owns the principle.** If a strategy change ships without decision-step instrumentation, `transparency-agent` blocks it.
- **`logging-specialist` owns the log schema.** If logging leaves out a reasoning field, `logging-specialist` blocks it.
- **`ux-designer` + `frontend-dev` own the UI.** If the trade-detail view hides or flattens the reasoning, `ux-designer` blocks at review.
- **`qa-manager` owns the QA frame** — requires every component to test its "emits reasoning" contract.
- **`software-architect`** ensures the instrumentation is architecturally clean, not bolted on — reasoning emission is part of the component protocol, not an optional extra.

## Anti-patterns

- **"Black-box" signal functions** that return True/False without emitting the reasoning. **Reject.** Every decision function returns a structured reasoning object.
- **Logs that say "trade entered" without saying WHY.** Block at QA.
- **UI views that summarize** ("3 signals fired") without detail. Users must be able to drill into each signal.
- **Components that short-circuit** in ways that hide their contribution. Every evaluated component reports its outcome even if another component overrode it.

## Integration with config toggles

When the UI shows which components fired, it also shows which components were **available but disabled** via config toggles (see `concepts/architecture/configurable-strategy-components.md`). This helps `strategy-architect` + Haim quickly answer "would this trade have been different if X was on?"

## Interaction with other canonical pages

- [[concepts/architecture/configurable-strategy-components|configurable-strategy-components]] — components emit reasoning; toggles surface in the view
- [[concepts/architecture/config-lineage|config-lineage]] — config link per trade; diff view
- [[concepts/architecture/reusable-tools|reusable-tools]] — graph tool is the visualization primitive
- `/opt/traderb/openclaw/PROJECT_VISION.md` §Human oversight — the "no black box" principle this page operationalizes (outside wiki vault)
- [[sources/coordination-rules|coordination-rules]] — `qa-manager` frames QA; `software-architect` enforces clean instrumentation

## What this is NOT

- Not a requirement for post-hoc explanation generation by an LLM. Reasoning is structured at decision time, not retrofitted.
- Not a requirement for the bot to explain in prose. Structured data + rendered UI is enough. Prose is optional polish.
- Not a debugging console. This is trader-facing. It shows reasoning, not internals.

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->
