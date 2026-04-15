---
pageType: source
id: source.index
title: index
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/process/runbooks/index.md
ingestedAt: 2026-04-15T22:48:04.441Z
updatedAt: 2026-04-15T22:48:04.441Z
status: active
---

# index

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/process/runbooks/index.md`
- Bytes: 4155
- Updated: 2026-04-15T22:48:04.441Z

## Content
```text
---
pageType: concept
id: concept.process.runbooks.index
title: Runbooks — Operational Step-by-Step Procedures
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Runbooks

> Operational checklists for actions we execute rarely enough that rederiving is risky. Each runbook is paired with a policy page (the "why"); the runbook itself is the "how."

## The difference between a runbook and a policy page

- **Policy pages** (in `concepts/process/<topic>.md`) describe rules, approval bars, who-does-what. They are the source of truth for how the team operates.
- **Runbooks** (in `concepts/process/runbooks/<topic>.md`) are executable — copy, paste, verify. They include exact CLI commands, expected output at each step, rollback paths, commit message templates.

Policy changes rarely. Runbooks change when the underlying tooling changes.

## Available runbooks

### Persona lifecycle
- `persona-add.md` — add a new persona (full procedure including OpenClaw CLI, doc-update chain, smoke test, rollback)
- `persona-remove.md` — retire a persona (reassign in-flight work, archive workspace, full doc chain, rollback)
- `persona-adapt.md` — update an existing persona's SOUL / AGENTS / relationships (both material and minor paths)

Policy home: [[concepts/process/persona-lifecycle|persona-lifecycle]]

### (Future runbooks expected as the team grows)
- `add-new-skill.md` — install and enable a ClawHub skill
- `rotate-broker-adapter.md` — swap prop firm / broker integration
- `full-vault-lint-and-cleanup.md` — periodic wiki maintenance pass
- `recover-from-vps-failure.md` — disaster recovery from GitHub mirrors
- `sprint-kickoff.md` — standard sprint kickoff sequence

These are placeholder entries — write each when the first real need arises, not speculatively.

## Runbook authoring conventions

When writing a new runbook, follow this structure so they're uniform and scannable:

1. **Frontmatter** — matches policy-page pattern (`pageType: concept`, id under `concept.process.runbooks.<slug>`)
2. **One-line purpose** with a pointer to the policy page it executes
3. **Prerequisites (gate)** — explicit checkboxes. Do not start unless all are checked.
4. **Stage 1 vs steady-state executor note** — who runs it
5. **Steps — numbered, with:**
   - Exact command(s) in code blocks
   - Expected output
   - Verification step
   - Rollback note per step when applicable
6. **Single-commit rule section** — every runbook that touches multiple files must show the commit message template
7. **Verification section** (post-execution smoke test)
8. **Rollback section** — how to reverse if things went wrong
9. **Common issues** — symptom / likely cause / fix table
10. **References** — policy page, OpenClaw docs, related runbooks

Keep each runbook under ~200 lines if possible. If a runbook naturally exceeds that, split it into sub-runbooks.

## Who owns runbooks

- **`project-lead` (post-Stage-1)** is the primary executor and owns runbook currency (keep them in sync with OpenClaw CLI changes and actual process evolution).
- **Admin scribe (during Stage 1)** stands in for `project-lead`.
- **Any persona** may propose updates via handoff if they notice drift.
- **Haim approves** any change to a runbook that embeds new approval bars or changes the approval chain.

## Related

- [[concepts/process/persona-lifecycle|persona-lifecycle]] — the policy powering the persona runbooks above
- [[concepts/process/milestone-workflow|milestone-workflow]] — the broader process context
- [[concepts/process/task-kickoff-flow|task-kickoff-flow]] — how tasks get defined
- [[sources/coordination-rules|coordination-rules]] — enforcer matrix + escalation ladder + QA capacity

## Notes

- Runbooks are **single-source**: the version in the wiki is canonical. No copies in personal workspaces, no forks. Agents consult via `wiki_get concept.process.runbooks.<name>`.
- When OpenClaw CLI signatures change (upgrades introduce new flags, deprecate old ones), the affected runbook gets an update commit with a brief note in the commit message. Don't leave stale commands in place.

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Generated
<!-- openclaw:wiki:sources:index:start -->
- [[sources/broker-portability|broker portability]]
- [[sources/code-quality-principles|code quality principles]]
- [[sources/config-lineage|config lineage]]
- [[sources/configurable-strategy-components|configurable strategy components]]
- [[sources/coordination-rules|coordination rules]]
- [[sources/documentation-hygiene|documentation hygiene]]
- [[sources/milestone-workflow|milestone workflow]]
- [[sources/persona-adapt|persona adapt]]
- [[sources/persona-add|persona add]]
- [[sources/persona-lifecycle|persona lifecycle]]
- [[sources/persona-remove|Persona Remove]]
- [[sources/project-management-methodology|project management methodology]]
- [[sources/reusable-tools|reusable tools]]
- [[sources/role-briefs-research|Role Briefs — Research]]
- [[sources/runbook-adapt-a-persona|Runbook: Adapt a Persona]]
- [[sources/runbook-add-a-persona|Runbook: Add a Persona]]
- [[sources/runbook-remove-a-persona|Runbook: Remove a Persona]]
- [[sources/runbooks-index|Runbooks Index]]
- [[sources/scope-discipline|scope discipline]]
- [[sources/scope-discipline-for-coding-tasks|Scope Discipline for Coding Tasks]]
- [[sources/strategy-explainability|strategy explainability]]
- [[sources/task-kickoff-flow|task kickoff flow]]
- [[sources/teams-and-squads|teams and squads]]
- [[sources/template|template]]
- [[sources/traderb-team-roster|TraderB Team Roster]]
- [[sources/trading-numbers-discipline|trading numbers discipline]]
- [[sources/vocabulary-and-naming|vocabulary and naming]]
- [[sources/wiki-information-architecture|Wiki Information Architecture]]
<!-- openclaw:wiki:sources:index:end -->
