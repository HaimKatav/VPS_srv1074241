---
pageType: source
id: source.template
title: template
sourceType: local-file
sourcePath: /opt/traderb-wiki/entities/components/_template.md
ingestedAt: 2026-04-15T22:44:49.617Z
updatedAt: 2026-04-15T22:44:49.617Z
status: active
---

# template

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/entities/components/_template.md`
- Bytes: 7505
- Updated: 2026-04-15T22:44:49.617Z

## Content
````text
---
pageType: entity
id: entity.components._template
title: Component Entity — Template
status: active
approved-by-haim: true
domain: components
updatedAt: 2026-04-15
---

# Component Entity — Template

> Copy this template when creating a new component entity page under `entities/components/<slug>.md`. Pages are created **just-in-time** by `project-lead` when an enforcer's [[concepts/process/task-kickoff-flow|task-kickoff-flow]] Step 2 breakdown first touches a component.

Not every section is filled at first creation. Empty role-sections are normal — they populate over time as different tasks touch the component and different role-owners contribute their angle.

Per [[concepts/meta/documentation-hygiene|documentation-hygiene]] Rule 6: this template uses **roles** (agent-ids), not persona names. The persona name gets logged in the `last-updated` HTML comment of each section as a record of who held the role when the contribution was made.

---

## Template — replace the placeholders

```markdown
---
pageType: entity
id: entity.components.<slug>
title: <Canonical Human Name>
status: active
approved-by-haim: true
domain: components
updatedAt: YYYY-MM-DD
---

# <Canonical Human Name>

## Canonical identity

- **Canonical name:** <human-readable name — see [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]] §Rule 1>
- **Code identifier(s):** <class names, module paths, CSS classes>
- **Location(s) in codebase:** <file paths where the component lives>
- **Consumers:** <which app surfaces use this — Dashboard, Observatory, Debug Browser, etc.>

## Role angles (fill in as work touches each lens)

Each section below is owned by the named role. Populated during [[concepts/process/task-kickoff-flow|task-kickoff-flow]] Step 2 breakdowns. Each entry is timestamped with the persona name at time of contribution for audit — per [[concepts/meta/documentation-hygiene|documentation-hygiene]] Rule 6.

### strategy-architect — semantic meaning
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: strategy-architect, task: <ticket-id> -->

What this component represents at a trading-logic level. Calculations shown, why specific values. References to research papers or internal decisions that backed the numbers. If this involves trading numbers, apply [[concepts/meta/trading-numbers-discipline|trading-numbers-discipline]].

### python-dev — implementation
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: python-dev, task: <ticket-id> -->

Where the logic lives. Data flow. Dependencies. Entry points. Testing hooks.

### frontend-dev — UI implementation
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: frontend-dev, task: <ticket-id> -->

HTML structure. CSS classes. JS behaviors. Event wiring. Responsiveness notes.

### ux-designer — design rationale
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: ux-designer, task: <ticket-id> -->

Why these design choices. Information hierarchy. Interaction patterns. Edge-case behaviors. What a trader needs to see first.

### data-engineer — data pipeline angle
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: data-engineer, task: <ticket-id> -->

Data sources feeding this component. Schema. Update frequency. Replay vs live differences (if any). Parity implications.

### logging-specialist — emitted events
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: logging-specialist, task: <ticket-id> -->

What events this component emits. Log shape. What downstream queries consume these events.

### qa-manager — QA scope (executed by qa-tester)
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: qa-manager, task: <ticket-id> -->

What must be tested for this component. Coverage requirements. Edge cases. Regression fixtures (if applicable). Parity requirements (if trading-touching).

### transparency-agent — explainability fit
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: transparency-agent, task: <ticket-id> -->

How this component contributes to trader-facing explainability. What reasoning it exposes. References to [[concepts/ux/strategy-explainability|strategy-explainability]].

### security-specialist — if applicable
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: security-specialist, task: <ticket-id> -->

If this component crosses a trust boundary (API call, credential handling, data export): secrets handling, input validation, output scrubbing.

### risk-manager / compliance-officer — if trading-touching
<!-- last-updated: YYYY-MM-DD, by: <persona-name>, role: <risk-manager | compliance-officer>, task: <ticket-id> -->

If this component affects trade decisions, money flow, or prop-firm rule enforcement: risk implications, rule coverage, blast-radius analysis.

## Related components

- [[entities/components/<related-1>|<Related Component 1>]]
- [[entities/components/<related-2>|<Related Component 2>]]

## Tasks that touched this component (log, most recent first)

- YYYY-MM-DD — <ticket-id> — <persona-name> (role: <role>) — <one-line summary of what changed>
- YYYY-MM-DD — <ticket-id> — <persona-name> (role: <role>) — …

## Related canonical pages

- [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]] — why this name
- [[concepts/meta/documentation-hygiene|documentation-hygiene]] — Rule 6 + verification chain
- [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — how this page gets updated
```

---

## Rules for using this template

1. **Copy-paste this template** when creating a new component page. Do not reinvent structure.
2. **Fill only the sections relevant to the first task** touching the component. Leave other sections with placeholder markers (the timestamp comment + a "not yet documented" note).
3. **Update sections across time** — each role-owner updates their own section when their work touches the component. The timestamp at the top of each section shows when it was last refreshed + the persona name at the time of that update (per [[concepts/meta/documentation-hygiene|documentation-hygiene]] Rule 6).
4. **Stale sections** (timestamp older than ~6 months) get flagged by `wiki lint` for a review. Stale = maybe the component evolved but the section wasn't updated = risk.
5. **Contradictions across updates** — same section updated with conflicting information — surface to `project-lead` for the [[concepts/meta/documentation-hygiene|documentation-hygiene]] drift-resolution flow. Per Haim's area-of-control rule: investigation focuses on the role whose domain actually owns that fact; the persona name at time of each claim is part of the audit.

## Example pages (populated as we build)

None yet — first real component entity page lands when Stage 3 (NQ migration) actually begins work on components. Until then, the template stands alone.

## Interaction with other canonical pages

- [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — enforcers identify component touches in Step 2; `project-lead` creates/updates these pages during Step 4 collation
- [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]] — canonical-name discipline
- [[concepts/meta/documentation-hygiene|documentation-hygiene]] — Rule 6 + verification chain + drift resolution
- [[concepts/meta/trading-numbers-discipline|trading-numbers-discipline]] — applies to any section that references trading numbers
- [[concepts/architecture/reusable-tools|reusable-tools]] — well-formed components often ARE reusable tools

````

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
