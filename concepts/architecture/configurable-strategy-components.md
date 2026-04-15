---
pageType: concept
id: concept.architecture.configurable-strategy-components
title: Configurable Strategy Components
status: active
approved-by-haim: true
domain: architecture
updatedAt: 2026-04-15
---

# Configurable Strategy Components

## The principle

Every strategy component (supply/demand zones, liquidity detection, FVG, mean reversion, trailing logic, etc.) exposes an **enable/disable toggle via config**. The bot reads config at start; the UI surfaces the toggles. Strategy variants can be run without code changes.

This is a first-class requirement, not an afterthought.

## Why

- **A/B testing without redeploys.** Run strategy V3 with FVG on against strategy V3 with FVG off. Same code, two configs.
- **Fast regression isolation.** A new release underperforms? Disable the component added last release, see if performance recovers. Root-cause in minutes, not sprints.
- **Backtesting strategy combinations** against the same fixture days using different configs — pure, deterministic comparison.
- **Haim + Noa tweak strategy directly** without waiting for Lior to push code. Config changes are reversible; code changes are not.
- **Every running configuration is captured** per `concepts/architecture/config-lineage.md` — so every trade can be traced back to the exact component mix that produced it.

## What this requires at implementation time

- **Every strategy component has a clear config key.** E.g. `strategy.components.fvg.enabled`, `strategy.components.liquidity_detector.enabled`.
- **The main signal engine reads the flags and dispatches accordingly.** `if config.strategy.components.fvg.enabled: self._run_fvg()`.
- **Components don't read config directly.** The signal engine owns config interpretation; components accept their parameters via constructor. Clear seam.
- **The UI has a controls panel** listing every toggle with live on/off state. Haim can flip a toggle and see it take effect on next session start (or next restart — whichever the config-reload policy allows).
- **Every trade record carries a `config_hash` reference** (see `concepts/architecture/config-lineage.md`) so we can reconstruct which components were active when the trade fired.
- **Component-specific parameters are also configurable**, not just on/off. E.g. `strategy.components.fvg.min_gap_size_ticks = 3` is as configurable as the on/off flag.

## What this is NOT

- **Not a plugin system.** Components still live in the main codebase; they're not dynamically loaded. "Configurable" means config-toggleable, not dynamically installable.
- **Not unrestricted runtime mutation.** Config loads once at bot start (or per-session). We don't hot-swap components mid-session — that's a parity risk.
- **Not a license to add half-baked components.** Every component going into the config-toggleable set has its own tests, its own documentation, and its own retirement path.

## Open design question (pending ADR)

- **The exact representation of config** — nested JSON, YAML, Pydantic dataclass, envvar-first — is an architectural decision Idan + Noa produce an ADR for when implementation approaches. Principle stated here; mechanism TBD.
- Likely shape: Pydantic dataclass with JSON serialization for persistence. Human-editable via Observatory UI.

## Enforcement

- **Idan reviews** every new strategy component at Step 6 (task-kickoff-flow): does it expose a toggle from day one? Is the toggle integrated into the signal engine's dispatch? Does it write a distinct config section?
- **Noa owns the semantics** of each component — "when should this be on? what does 'off' mean for this component? is it required under what conditions?"
- **Guy (qa-manager) frames QA** to include: the component works when enabled, the component is genuinely inert when disabled (no side-effects leaking into other components), the config persists + reloads correctly.

## Interaction with other canonical pages

- [[concepts/architecture/config-lineage|config-lineage]] — toggles captured in the config snapshot; every trade knows which toggles were on
- [[concepts/architecture/broker-portability|broker-portability]] — strategy components are broker-agnostic; toggles apply equally across brokers
- [[concepts/ux/strategy-explainability|strategy-explainability]] — UI surfaces which components fired for a given trade (requires toggles to be visible)
- [[sources/coordination-rules|coordination-rules]] — changes to trading-touching configuration pass through Omer (risk) + Shira (compliance) at step 2 of task-kickoff-flow

## Anti-patterns that violate this principle

- **Hard-coded `if True:` around a component** because someone "turned it off temporarily." Make it a toggle; the config is where temporary-off belongs.
- **A component with no off-switch.** Every component must be disable-able. If it's "too critical to disable," that's a warning sign the component belongs in core, not in the strategy-components layer.
- **Silent defaults.** Every config key has an explicit default documented; don't rely on Python's `None`.
