---
pageType: source
id: source.broker-portability
title: broker portability
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/architecture/broker-portability.md
ingestedAt: 2026-04-15T20:54:11.883Z
updatedAt: 2026-04-15T20:54:11.883Z
status: active
---

# broker portability

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/architecture/broker-portability.md`
- Bytes: 4897
- Updated: 2026-04-15T20:54:11.883Z

## Content
```text
---
pageType: concept
id: concept.architecture.broker-portability
title: Broker Portability
status: active
approved-by-haim: true
domain: architecture
updatedAt: 2026-04-15
---

# Broker Portability

## The invariant

Every broker integration goes through the same two protocols: **`BrokerClient`** and **`MarketDataFeed`**. Strategy code, risk code, compliance code, and logging code never reach past these protocols into a specific broker's internals.

Adding a new broker = implement two protocols + tests. Zero changes to strategy, risk, compliance, logging, UI. That is the contract.

## Why this matters for TraderB

- **Tradovate is our first concrete implementation** (Phase 1 focus per `PROJECT_VISION.md`).
- **TopstepX / Apex / future prop firms** will be added via the same protocols in later phases.
- **Final prop firm choice is deferred** — we cannot afford to couple to a specific vendor prematurely.
- **Swapping brokers mid-development requires zero bot-logic rework.** This is explicitly a product requirement, not just nice-to-have.

## What the protocols guarantee

### `BrokerClient`
- Order submission (market, limit, bracket) with idempotent behavior
- Order lifecycle: submitted → accepted → filled / rejected / cancelled
- Account state queries (positions, margin, realized/unrealized P&L)
- Authentication + session management contract

### `MarketDataFeed`
- Tick subscription for configured instruments
- Deterministic tick ordering (critical for `concepts/architecture/config-lineage.md` and parity)
- Disconnect/reconnect semantics
- Historical replay capability using the same shape as live (parity foundation)

## What this guarantees downstream

- **Strategy code** never imports a specific broker module. It takes a `BrokerClient` via dependency injection.
- **Risk code** never knows which broker it's operating under. Broker-agnostic rules only.
- **Compliance code** CAN vary by broker (prop firms have different rules — see `PROJECT_VISION.md` §Prop Firm Strategy) but goes through a parameterized rule-enforcement layer, not hard-coded constants per broker.
- **Logging + telemetry** emit broker name as a tag, never as a code path.
- **Contract tests** run against every broker implementation using the same test set.

## Anti-patterns that violate the invariant

- `if isinstance(broker, TradovateClient): ...` — pattern matching on concrete broker type in strategy code. **Reject.**
- Importing `brokers.tradovate_adapter` from anywhere outside `brokers/`. **Reject.**
- Broker-specific conditionals in risk gates. **Reject** — parameterize the rule instead.
- Exposing broker internals through the protocol (leaky abstraction). **Reject** — tighten the protocol.

## Adding a new broker — the procedure

1. Shira (compliance) produces the rule parameterization for the new firm (daily loss limits, max contracts, consistency, drawdown tracking — per the firm's current rulebook).
2. Dan (data-engineer) reviews the broker's market data characteristics for parity compatibility.
3. Lior (python-dev) implements `BrokerClient` + `MarketDataFeed` for the new broker in `brokers/<name>_adapter.py`.
4. Idan (software-architect) reviews for protocol conformance — no leaks, no broker-specific assumptions creeping upstream.
5. Guy (qa-manager) frames a QA scope that includes: contract tests pass, parity snapshots pass on replay-of-live-data, compliance rules load correctly for the new firm.
6. Tamar (security-specialist) reviews auth + API credential handling.
7. Ari schedules and ships as a milestone.

## When to revisit the protocol itself

The protocols will evolve. When a new broker needs something no existing protocol method covers:
- Extend the protocol (add a new method)
- Default implementation in the base for backward compatibility
- Document the extension in an ADR under `concepts/architecture/adrs/`
- Every existing broker's adapter gets the new method (or a documented no-op if truly N/A)

Never fork the protocol per broker. One protocol, many implementations.

## Related canonical pages

- [[concepts/coding/code-quality-principles|code-quality-principles]] — the broker abstraction is the canonical seam example
- [[concepts/architecture/reusable-tools|reusable-tools]] — the protocol IS a reusable tool
- [[concepts/architecture/configurable-strategy-components|configurable-strategy-components]] — strategy components that take a `BrokerClient` parameter
- `/opt/traderb/openclaw/PROJECT_VISION.md` §Prop Firm Strategy — why we stay firm-agnostic (outside wiki vault)

## Existing implementation reference

- `brokers/` directory, specifically the `BrokerClient` + `MarketDataFeed` protocol definitions
- `brokers/tests/` — contract test suite runs against every implementation
- Current adapters: alpaca (being deprecated in Phase 1), tradovate (Phase 1 target), replay (always), paper (always)

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
