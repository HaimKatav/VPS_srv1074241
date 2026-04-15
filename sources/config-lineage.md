---
pageType: source
id: source.config-lineage
title: config lineage
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/architecture/config-lineage.md
ingestedAt: 2026-04-15T22:48:03.830Z
updatedAt: 2026-04-15T22:48:03.830Z
status: active
---

# config lineage

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/architecture/config-lineage.md`
- Bytes: 5624
- Updated: 2026-04-15T22:48:03.830Z

## Content
```text
---
pageType: concept
id: concept.architecture.config-lineage
title: Config Lineage
status: active
approved-by-haim: true
domain: architecture
updatedAt: 2026-04-15
---

# Config Lineage

## The principle

Every trade and every session carries a **`config_hash`**. That hash points to an **immutable config snapshot**. Given any trade, we can reconstruct the exact configuration the bot was running under when it decided to take that trade — which components were enabled, what parameters they had, which firm's rules were in force, and anything else the config covers.

No trade exists without a `config_hash`. No config was used without a snapshot.

## Why this is sacred

- **Analysis.** We need to know which config produced which P&L. Without lineage, comparing sessions is guesswork and retrospectives are noise.
- **Parity** (the invariant). Regression testing must replay with the exact config that was live. Floating configs = parity drift = the sacred invariant breaks.
- **Audit.** Prop firm investigations may require showing the ruleset that was in force at a specific moment. "We were running strategy V3.2 with FVG off and the Tradovate $50k ruleset" — provable, not vibes.
- **Rollback.** If strategy V7 loses money, we rollback to the hash that was winning. Specific config, specific hash, specific state.
- **Scientific iteration.** A strategy change is falsifiable only if we can diff the before-config against the after-config.

## Requirements

### Immutable snapshots
- When a config is used for a run, a snapshot is written to disk.
- Snapshots are **never overwritten or edited**.
- Stored under `config_snapshots/<hash>/` with the config as canonicalized JSON + a `manifest.json` describing provenance (who committed, when, what branch, parent hash if derived).
- Hash = SHA-256 of canonicalized JSON.

### Config hash in every trade record
- Every trade row in the run log has a `config_hash` field.
- Every session start writes a session record with `config_hash` + timestamp + broker + mode (live / paper / replay).
- Logs (per `logging-specialist`'s logging schema) include `config_hash` in every tick-level event for fast lookup.

### UI access
- From a trade view in the Dashboard or Observatory, Haim can click into the config snapshot that produced the trade.
- Config snapshots are diff-able against each other in the UI (`software-architect` or `frontend-dev` will own the diff tool at implementation time — see `concepts/architecture/reusable-tools.md`).

### Named config sets
- Users can save configs with human-readable names (`strategy_v3_aggressive`, `baseline_noFVG`, `topstep_evaluation_setup`) that resolve to a specific hash.
- Name → hash mapping stored in `config_snapshots/_names.json`.
- Names are mutable (you can rename), but the underlying hash + snapshot is immutable.

## Existing infrastructure (TraderB already has the bones)

- `config_snapshots/` directory exists in the code tree.
- Current implementation stores snapshots per-run but hasn't formalized the lineage contract across every trade and session.
- Formalization is a Phase 2 (Testing Parity) deliverable — parity verification REQUIRES config lineage to function.

## Enforcement

- **`software-architect`** reviews any change to config-reading or config-writing code — lineage contract must not be broken.
- **`data-engineer`** owns the storage format + retention policy for snapshots.
- **`logging-specialist`** ensures every logged event carries the `config_hash` context.
- **`transparency-agent`** ensures the UI surface actually exposes config-per-trade, not hidden behind a click-through.
- **`qa-manager`** frames QA that includes: "trade record without config_hash = automatic fail; config_hash without snapshot = automatic fail; snapshot modified post-run = automatic fail."

## Anti-patterns that violate lineage

- **Config modified in-place** after a run started. **Forbidden.** Start a new session with the new config.
- **Computed config** (e.g. values derived at runtime from environment variables not captured in the snapshot). Every effective parameter ends up in the snapshot, including resolved env vars.
- **Trade records without config_hash.** Caught by QA framing, blocked at merge.
- **Manual edits to `config_snapshots/` files.** Hard rule, enforced via file permissions + git.

## Retention policy

- **Keep all snapshots indefinitely.** Storage cost is negligible compared to the audit value.
- **Named configs survive** even if the named config is "replaced" — the old hash is still reachable.
- **Git-tracked** — `config_snapshots/` is in the code repo (not a separate data store) so backups + history are free.

## Interaction with other canonical pages

- [[concepts/architecture/configurable-strategy-components|configurable-strategy-components]] — the toggles this tracks; lineage records which components were active
- [[concepts/ux/strategy-explainability|strategy-explainability]] — UI exposes config per trade so "why did the bot decide X?" has a complete answer
- [[concepts/architecture/broker-portability|broker-portability]] — config includes which broker, which firm ruleset; lineage captures that too
- `/opt/traderb/openclaw/PROJECT_VISION.md` §Parity is sacred — lineage is a foundational mechanism enabling parity (outside wiki vault)

## What this is NOT

- Not an audit log of *edits* to config (that's git history). It's an audit log of what *ran*.
- Not a backup system for config files. It's a lineage record of what the bot read on session start.
- Not a feature flag service — it's a snapshot of the flags' state per session.

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
