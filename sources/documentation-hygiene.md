---
pageType: source
id: source.documentation-hygiene
title: documentation hygiene
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/meta/documentation-hygiene.md
ingestedAt: 2026-04-15T22:43:53.858Z
updatedAt: 2026-04-15T22:43:53.858Z
status: active
---

# documentation hygiene

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/meta/documentation-hygiene.md`
- Bytes: 9242
- Updated: 2026-04-15T22:43:53.858Z

## Content
```text
---
pageType: concept
id: concept.meta.documentation-hygiene
title: Documentation Hygiene
status: active
approved-by-haim: true
domain: meta
updatedAt: 2026-04-15
---

# Documentation Hygiene

> The team's DNA for writing and maintaining canonical knowledge. Every doc submitted to the vault upholds these rules — it's each enforcer's job to verify before submission. `project-lead` is the final gatekeeper before content lands in the wiki.

## Project mentality (top of every contributor's mind)

- **This project is for humans.** Every output, every doc, every trade, every UI surface — all oriented for human verification. Haim must be able to look at any artifact and understand it.
- **We are here to ship.** Rigor in service of outcome, not rigor for ceremony. Every rule on this page exists because drift downstream costs us. Keep the purpose in view.
- **Everyone acts as if this product is their baby.** Learn it, care about it, strive for the best version. Not "good enough to pass review" — the best the persona can produce given the role.

## The DNA — five rules in every doc

### Rule 1 — Canonical names everywhere

Per [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]]: one name per thing, used consistently. An enforcer reviewing a submission checks for synonym drift and requires corrections before signing off.

### Rule 2 — Clusterable connections via wikilinks

Every cross-reference to another canonical page uses Obsidian wikilinks: `[[path|alias]]`. Path-only references are reserved for files outside the wiki vault.

### Rule 3 — Minimal loading

Pages are scoped tight — ONE concept per page. A page that covers three things splits into three. Each page short enough that an agent can load it without context bloat.

### Rule 4 — Area-of-control tagging

Every fact, claim, or decision in a doc is tagged — implicitly or explicitly — with the persona whose area-of-control owns it. When a contradiction is detected, investigation routes to the rightful owner.

**The area-of-control rule (Haim's formulation, 2026-04-15):**
- Every fact has a rightful owner — the persona whose role covers that fact.
- Contradictions are investigated within the owner's lane; other personas' opinions on that fact don't count.
- Cross-validation during investigation: review QA test results + Dev test results as part of establishing which version is correct.
- **Haim judges.** Escalation per [[sources/coordination-rules|coordination-rules]] §Certainty discipline.

### Rule 5 — Human-verification required

Every significant claim or decision in a doc must be reviewable by a human — ideally traceable to a source (code, research, prior decision, Haim sign-off). Opaque claims with no traceability get flagged for enforcer verification.

### Rule 6 — Roles in instructions, names in records

**Rule:** when a document describes rules, instructions, ownership, structure, or sequential operations, refer to the **role** (agent-id: `project-lead`, `software-architect`, `qa-manager`, etc.) — never the persona's human name. Names are reserved for logs, records, and audit trails where we need to know *who was holding the role at the time*.

**Why:** personas change. We may rename agents, swap who holds a role, or spin up a new project using the same infrastructure with a different crew. Instructions written in role terms survive these changes; instructions written in human names don't.

**Specifics:**
- **Rules + instructions + ownership language + sequential operations + structure descriptions** → role (agent-id)
- **Logs + decision records + PM task history + meeting records + retro notes** → persona name (at time of action)
- **Roster pages** (`entities/team/index.md`) → names + roles explicitly paired — this page IS the lookup
- **Examples** may use names for concreteness when marked as *"Example: ..."* — the framing makes the name non-prescriptive
- **Tense matters:** present/future-tense instruction → role; past-tense specific event → name
- **Haim** is the actual human, not a persona — referred to as Haim throughout

**Enforced by:** every enforcer verifying a submission; `project-lead` at the coordination review. Pages violating Rule 6 are corrected before ingestion, not after.

**Short role names in prose:** agent-ids are canonical (`software-architect`), but prose can use short forms where clarity wins (e.g. `architect`, `PM`, `qa-manager` stays as-is since it's short). Canonical agent-id for formal references; short form acceptable in casual instruction flow. The test: would swapping names preserve meaning without editing the sentence? If yes, use role.

## The verification cascade

Every submission to the vault passes through this chain. Each step is required; skipping undermines the next.

### Step 1 — Worker writes
The persona performing the task writes the document as part of their work (breakdown, spec, decision, ADR, whatever fits the task). They cite wiki pages they consulted, use canonical names, tag area-of-control.

### Step 2 — Enforcer verifies (from their domain lens)
The relevant enforcer (per [[sources/coordination-rules|coordination-rules]] §Enforcer matrix) reviews the submission. Specifically checks:
- Canonical names used consistently (Rule 1)
- Wikilinks where appropriate (Rule 2)
- Scope is tight, no kitchen-sink (Rule 3)
- Area-of-control tagging correct; facts in domains outside this enforcer's lane are flagged for the rightful owner (Rule 4)
- Claims traceable to source (Rule 5)
- Substance of the content is sound (the enforcer's domain expertise)

**The enforcer corrects what's wrong** — not sends it back with vague complaints. They also check their underlings' work in the same submission. Verification includes fixing.

### Step 3 — Enforcer submits to `project-lead`
Once the enforcer signs off, the submission is handed to `project-lead` via handoff.

### Step 4 — `project-lead` coordination review
`project-lead` reviews from the coordination lens (NOT from domain expertise — that's already covered by the enforcer):
- Is this coherent with existing vault content?
- Are wikilinks pointing at real pages?
- Is it in the right folder per [[concepts/meta/wiki-information-architecture|wiki-information-architecture]]?
- Are the ripple updates (if material change) included per the cascading-review rule in [[concepts/process/persona-lifecycle|persona-lifecycle]]?

### Step 5 — `project-lead` ingests + lints
`project-lead` writes to the canonical location, runs `openclaw wiki ingest`, then `openclaw wiki lint`.

### Step 6 — Drift resolution
If lint surfaces inconsistency (contradiction with existing content, orphan, staleness):
- `project-lead` packages the full case: which pages contradict, what each says, whose area-of-control covers the contested fact, what QA/Dev tests say (if applicable), 2–3 possible resolutions
- `project-lead` escalates to Haim
- Haim decides, or asks the responsible agents questions
- Once Haim decides, the decision becomes the new truth — ingested + `approved-by-haim: true`

## What counts as "significant"

Not every wiki update needs the full cascade. The enforcer's judgment + `project-lead` judgment — but when in doubt, cascade.

Clearly significant (cascade required):
- Any decision affecting trading logic, money, or prop-firm rules
- Any architectural pattern change
- Any new term, rule, or process codified
- Any persona lifecycle event

Clearly minor (`project-lead` direct):
- Typo fixes
- Link updates when a page moves
- Adding a wikilink where a path reference was
- Adding a timestamp to a persona-section that was populated

In-between cases: when in doubt, go full cascade. Haim-ticks are cheap; undetected drift isn't.

## Enforcement rhythm

- **Every submission gets verified** before landing — no exceptions. An enforcer who waves through a non-compliant submission breaks the chain.
- **Weekly `wiki lint`** via system cron + `project-lead` manual review of the report.
- **Stale-page detection** (per the [[entities/components/_template|components template]]'s timestamp pattern) catches sections that haven't been touched in too long.

## Interaction with other canonical pages

- [[concepts/meta/vocabulary-and-naming|vocabulary-and-naming]] — Rule 1 operationalized
- [[sources/coordination-rules|coordination-rules]] — the enforcer matrix this page uses + escalation ladder
- [[concepts/process/persona-lifecycle|persona-lifecycle]] — cascading-review rule that applies when persona changes
- [[concepts/meta/wiki-information-architecture|wiki-information-architecture]] — where pages land
- [[concepts/meta/trading-numbers-discipline|trading-numbers-discipline]] — extra discipline for numerical claims

## What this is NOT

- Not a replacement for domain expertise. The enforcer brings domain judgment; these rules make sure the artifact is well-formed beyond that.
- Not a delay mechanism. Verification happens fast — enforcers aren't allowed to sit on a submission indefinitely (per the escalation ladder).
- Not a consensus-seeking protocol. `project-lead` doesn't poll for opinions — he confirms the submission meets the rules and routes to Haim if drift surfaces.

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
