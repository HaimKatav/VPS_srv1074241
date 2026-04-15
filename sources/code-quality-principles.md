---
pageType: source
id: source.code-quality-principles
title: code quality principles
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/coding/code-quality-principles.md
ingestedAt: 2026-04-15T22:48:04.204Z
updatedAt: 2026-04-15T22:48:04.204Z
status: active
---

# code quality principles

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/coding/code-quality-principles.md`
- Bytes: 4689
- Updated: 2026-04-15T22:48:04.204Z

## Content
```text
---
pageType: concept
id: concept.coding.code-quality-principles
title: Code Quality Principles
status: active
approved-by-haim: true
domain: coding
updatedAt: 2026-04-15
---

# Code Quality Principles

> The baseline every line of code we ship must meet. Enforced by `software-architect` at review time. Non-negotiable.

## Readable and self-explanatory

- **Names are chosen so the reader does not need a dictionary.** `entry_price_per_tick` beats `epptk`.
- **Comments explain *why*, not what.** The code already shows what. If a comment is summarizing the line, it's redundant.
- **If a comment is needed to explain a clever line, the line is too clever.** Refactor for readability first.
- **No implicit clocks, no hidden globals.** Every time-dependent function takes its time source as a parameter; every state-reading function takes its state as a parameter.

## OOP where it fits

- **Domain modeling uses classes + dataclasses.** Behavior belongs with the data it acts on. `Trade`, `Config`, `Position` are classes with methods, not dicts with free functions.
- **Protocols / ABCs define seams.** The `BrokerClient` + `MarketDataFeed` protocols (`concepts/architecture/broker-portability.md`) are the canonical example.
- **Free functions are fine for pure transformations.** Not everything needs to be a class. `round_to_tick(price, tick_size)` is cleaner as a function.
- **No inheritance for code reuse.** Composition beats inheritance. Use protocols for polymorphism; use plain composition for sharing.

## Scalable and structured

- **Small functions, clear inputs and outputs.** A function that reads like prose is good; a function that needs line-numbers as signposts is too big.
- **Modules by responsibility, not by file type.** `brokers/` not `adapters/`. `risk/` not `validators/`.
- **No global mutable state.** State lives on `TradingBot` or dataclasses, never in module-level mutables.
- **A new developer should be able to open the repo and find the file they need without asking.** If the folder structure doesn't telegraph what's where, the structure is wrong.

## Reusable tools

- **When a capability has dual use or expected repeated use, extract it as a tool/component.** See `concepts/architecture/reusable-tools.md`.
- **One implementation, many consumers.** No copy-paste of logic between files.

## Testability by design

- **Every new module arrives with its tests.** No exceptions. The tests are part of the task's DOD, authored by the coder, scoped by `qa-manager`.
- **If code resists unit testing, the architecture is wrong — not the test.** Refactor for testability, don't contort the test.
- **Pure functions wherever possible** — easier to test, fewer surprises.

## Error handling

- **Never catch bare `except:`.** Catch specifically. Log with context. Handle or re-raise.
- **Don't swallow exceptions.** If you catch it, you own it — add a log line, take a recovery path, or raise something meaningful.
- **Expected-path errors are data, not exceptions.** Broker says "order rejected — insufficient margin"? That's not an exception; it's a known outcome. Exceptions are for the unexpected.

## Dependency discipline

- **Prefer standard library over external packages.** Justify every third-party dependency in the PR.
- **Every dependency has a security review** (`security-specialist` enforces). New packages can't land without sign-off.
- **Pin versions, pre-commit hooks verify.** No floating deps in trading code.

## Parity implications

- **Non-determinism is forbidden in trading paths.** Random number generators without seeds, unordered dict iteration affecting output, time-dependent logic without injected clocks — all block at review.
- **Code paths that feed both live and replay must be identical.** Any divergence = parity risk. Block at review; require ADR if unavoidable.

## Enforcement

- `software-architect` reviews every non-trivial PR against these principles.
- `make test` + `make regression` must pass before merge to `develop`.
- `project-lead` gatekeeps the sprint-level DOD: code quality issues get blocked as architectural debt, not ignored.

## Related canonical pages

- [[concepts/coding/scope-discipline|scope-discipline]] — the per-task confinement rule
- [[concepts/architecture/reusable-tools|reusable-tools]] — when to extract
- [[concepts/architecture/broker-portability|broker-portability]] — the canonical protocol-seam example
- [[concepts/architecture/configurable-strategy-components|configurable-strategy-components]] — runtime toggles without code changes
- [[sources/coordination-rules|coordination-rules]] — enforcer matrix that puts `software-architect` on every coding PR

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
