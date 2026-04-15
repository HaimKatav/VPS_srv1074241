---
pageType: source
id: source.reusable-tools
title: reusable tools
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/architecture/reusable-tools.md
ingestedAt: 2026-04-15T20:53:59.175Z
updatedAt: 2026-04-15T20:53:59.175Z
status: active
---

# reusable tools

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/architecture/reusable-tools.md`
- Bytes: 3818
- Updated: 2026-04-15T20:53:59.175Z

## Content
```text
---
pageType: concept
id: concept.architecture.reusable-tools
title: Reusable Tools
status: active
approved-by-haim: true
domain: architecture
updatedAt: 2026-04-15
---

# Reusable Tools

## The principle

When a capability has dual use or is expected to be used repeatedly across the system, build it as a **reusable tool or component** — not inline where it first appeared.

Duplication is the enemy. One implementation, many consumers.

## What qualifies as a "tool"

- Clear, self-contained API (inputs, outputs, side-effect contract)
- No hidden dependencies on the caller's context
- Usable in multiple app areas without modification
- Documented once, consumed many times
- Testable in isolation from any specific consumer

## Canonical example

**The graph tool** — a chart component with marker support and zoom-to-trade capability. It is used in:
- The Dashboard (recent trades view)
- The Observatory (session review)
- The Debug Browser (step-through of decisions)

One implementation, three consumers. Change the charting logic once, all three surfaces benefit. No duplicated chart code anywhere.

Other emerging candidates on TraderB:
- Tick-aligned time-series renderer
- Config-diff viewer (compares two config snapshots per `concepts/architecture/config-lineage.md`)
- Handoff file template generator

## When to extract (the decision rule)

- **A second consumer appears** for the same capability → extract NOW, before the code diverges.
- **A planning session identifies** a capability with 2+ consumers → design as a tool from day one.
- **Copy-paste of logic between files** → extract immediately, no exceptions.

## When NOT to extract

- **Premature abstraction** — one consumer, no second user on the horizon. Wait until the second arrives.
- **Abstraction for its own sake** — if the shapes are genuinely different across candidates, don't force a fit. The abstraction leaks.
- **Speculative generality** — "someday we might want X" is not a reason to extract. YAGNI.

## Design principles for a well-formed tool

- **Narrow API surface.** Fewer public methods, clearer intent.
- **Strict inputs.** Validate on the boundary; don't accept garbage and hope.
- **Predictable outputs.** Same input → same output. No hidden state.
- **Composable.** Tools should combine cleanly, not fight each other.
- **Versioned.** When the tool's API changes, bump the internal version marker so consumers can adapt.
- **Owned.** Every tool has a designated owner persona who reviews changes to it (usually the persona in whose domain the tool lives).

## Enforcement

- **Idan flags duplicated logic in review.** "You copied 30 lines from `brokers/tradovate_adapter.py` into `brokers/topstepx_adapter.py` — extract."
- **Builders consult this page before deciding to extract.** If unsure, handoff to Idan for a quick judgment call.
- **New tools get a brief ADR** in `concepts/architecture/` if they introduce a non-trivial pattern.

## Interaction with other canonical pages

- [[concepts/coding/code-quality-principles|code-quality-principles]] — reusable tools is a specific application of the "scalable and structured" principle there.
- [[concepts/architecture/broker-portability|broker-portability]] — the broker abstraction is the largest current reusable tool (the protocol seam is a tool for broker swap).
- [[concepts/coding/scope-discipline|scope-discipline]] — extracting into a tool is a refactor; refactors are first-class tasks, not smuggled into unrelated changes.

## What this is NOT

- Not a mandate to abstract everything upfront. Let the second consumer reveal the shape.
- Not a rule against functions — small pure helpers don't need to be "tools" with elaborate APIs.
- Not a performance optimization guide — extraction is about clarity + reuse, not speed.

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
