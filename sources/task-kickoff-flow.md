---
pageType: source
id: source.task-kickoff-flow
title: Task Kickoff Flow
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/process/task-kickoff-flow.md
ingestedAt: 2026-04-15T11:53:09.629Z
updatedAt: 2026-04-15T11:53:09.629Z
status: active
---

# Task Kickoff Flow

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/process/task-kickoff-flow.md`
- Bytes: 10581
- Updated: 2026-04-15T11:53:09.629Z

## Content
```text
---
pageType: concept
id: concept.process.task-kickoff-flow
title: Task Kickoff Flow
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Task Kickoff Flow

> How an approved spec becomes a set of coder tasks. Runs once per task, between step 5 (spec approved by Haim) and step 6 (implementation) of the milestone workflow.

## Purpose

The spec has been approved by Haim. It describes *what* the milestone must produce. It does NOT yet describe *what the enforcers need the coder to do in each domain* or *what specific tasks the coder should create*.

This page defines how that translation happens. It puts the **domain experts** in front of the coder — not the coder guessing what each domain needs.

## The flow

### Step 1 — Ari distributes the approved spec to relevant enforcers

Based on what the spec touches, Ari identifies which enforcers apply (see `concepts/coding/scope-discipline.md` §Enforcers required by domain) and distributes via individual handoffs:
- `openclaw/handoffs/ari-to-idan_<date>_<task-slug>.md` (always)
- Plus whichever of Rotem / Shira / Tamar / Omer / Dan / Gal are relevant

The handoff includes: the approved spec, the scope hints Ari sees, the expected output from this enforcer.

### Step 2 — Each enforcer produces a domain breakdown

Each enforcer, working independently first, analyzes the spec from their lens and produces a **domain breakdown**: what needs to happen in their domain to realize this spec.

Format of a domain breakdown (short handoff, ~30 lines):
- **Domain concerns identified** (e.g. Tamar: "this adds a new outbound API call; secrets management + input validation required")
- **Specific requirements** (e.g. Idan: "new adapter must implement `BrokerClient` protocol; tests go in brokers/tests/; no global state")
- **Constraints / guardrails** (e.g. Omer: "bracket-order changes must keep max-contracts enforcement intact")
- **Questions or unknowns** surfaced at this step — anything the enforcer can't decide alone

The breakdown goes back to Ari via handoff: `openclaw/handoffs/<enforcer>-to-ari_<date>_<task-slug>_breakdown.md`.

### Step 3 — Enforcers surface alignment or escalate

Once Ari has all enforcer breakdowns, he puts them in a shared handoff thread: `openclaw/handoffs/enforcers-alignment_<date>_<task-slug>.md`. All participating enforcers read each other's breakdowns.

**If the breakdowns are coherent** — no domain-vs-domain conflict — the enforcers confirm alignment in the thread. Proceed to Step 4.

**If two (or more) enforcers disagree on domain-substantive grounds** — e.g. Rotem's visual hierarchy requires a component structure that Idan says breaks the module boundary — this is NOT resolved by the enforcers among themselves. Two domain experts disagreeing IS the signal that something is not aligned at a higher level.

Ari immediately packages the disagreement into a structured escalation for Haim:
- The specific point of disagreement
- Each enforcer's position and reasoning
- Why each enforcer thinks the other's position doesn't work in their domain
- 2–3 possible resolutions, with the cost of each resolution in each affected domain
- Any research questions the agents couldn't answer

Haim decides (or asks for more research, or asks questions of the relevant agents — who must be able to answer). Haim's decision becomes a new wiki entry: see §Step 7 below. Then proceed to Step 4 with Haim's direction.

**Enforcers do not negotiate positions to fit each other.** They state their domain-expert view. Haim breaks ties.

### Step 4 — Ari collates demands into a unified task brief

Once alignment is achieved (or Haim has decided), Ari produces the unified task brief: `openclaw/handoffs/ari-to-<coder>_<date>_<task-slug>.md`. Contents:
- The approved spec (reference)
- The aligned enforcer demands, organized by domain
- The list of enforcers who signed off (each breakdown handoff is referenced)
- Any Haim decisions from the alignment step

This is the authoritative brief the coder will work from.

### Step 5 — Coder creates their tasks

The coder (Lior, Maya, Dan, or Gal depending on the work) translates the brief into concrete tasks on the project board. For each task:
- Title + DOD
- Touch-area map (per `concepts/coding/scope-discipline.md`)
- Tests to add
- Dependencies / ordering

The tasks are NOT executed yet. They are a plan.

### Step 6 — Coder pushes the tasks back up the chain

The coder sends the task list back to the enforcers (who defined the demands) for a round-trip approval: `openclaw/handoffs/<coder>-to-enforcers_<date>_<task-slug>_tasks.md`.

Each enforcer replies **approved** or **revise: <specific concern>**. Revisions loop:
- Up to **3 revise rounds**.
- If the coder and an enforcer can't agree in 3 rounds, Ari escalates to Haim using the same structured format as Step 3.

When all enforcers reply **approved** on the current task list, proceed to Step 6.5 (not directly to coding).

### Step 6.5 — CI/CD preparation (git + pipeline readiness)

After all enforcers have approved the task list and BEFORE any code is written, the git environment is prepared. This is owned by Eitan (devops / CI-CD), split into three models by what the task touches:

| Task touches… | Branch creation | Model |
|---|---|---|
| Trading logic, broker adapters, risk rules, prop-firm compliance code, money-adjacent paths | **Eitan** creates the branch and hands off to the coder | Gate model |
| Everything else (UI polish, docs, non-trading modules, internal tools, observatory) | **Coder** creates the branch per Eitan's standing policy | Policy model |
| Trivial fixes (typo, single-line comment, doc nit) per Ari's judgment | **Coder** commits on the standing chore branch per Eitan's policy | Overkill-exempt |

**Standing policy** (maintained by Eitan in `concepts/coding/git-policy.md` — to be written):
- Branch naming: `feature/<ticket-id>-<short-kebab-desc>`
- Base branch: `develop` (never branch off `main`)
- No direct push to `develop` or `main` — protected
- CI runs automatically on all feature branches
- Revert path: revert the merge commit from `develop` OR squash-revert the feature branch

**Auditability — who created every branch, always:**
- The person creating the branch (Eitan in gate model, coder in policy model) makes an initial commit: `chore: open feature branch for <ticket-id>`. This captures authorship in git history permanently.
- They push the branch immediately so the remote has the creator's identity.
- **For gate-model branches** (trading-touching): Eitan additionally writes a confirmation handoff: `openclaw/handoffs/eitan-to-coder_<date>_<task-slug>_branch-ready.md`. This is the auditable paper trail for money-adjacent code.
- **For policy-model branches**: the coder's initial commit + their earlier task-approval handoffs from enforcers form the chain.

**What Eitan confirms before coding starts (both models):**
- Base branch is clean and current
- Branch protections active
- CI armed on the new branch
- For gate model: no conflict with other in-flight branches touching the same files

**Only after this step** — coding begins. Scope discipline (see `concepts/coding/scope-discipline.md`) applies from that moment.

**Escalation:** if Eitan identifies a git/CI issue that would block the task (merge conflict in base, CI needing a new stage, etc.), he handles it directly and coordinates with Ari on sprint timing. If a coder on the policy-model path can't follow the standing policy, they handoff to Eitan for assistance per the escalation ladder.

### Step 7 — Haim approval, when it happens, becomes new truth

Whenever the flow above escalates to Haim (deadlock in Step 3 or Step 6, or a net-new question Haim has never answered before):

1. Haim reviews the structured report and decides — or asks for more research, or asks the relevant agents questions. The relevant agents must be able to answer.
2. Once Haim decides, Ari ingests the decision into the wiki:
   - If it's a rule or principle → `concepts/<domain>/<slug>.md`
   - If it's a named thing → `entities/<domain>/<slug>.md`
   - If it's a specific spec or synthesis → `syntheses/<domain>/<slug>.md`
3. The page is flagged `approved-by-haim: true` at the moment of ingestion.
4. `openclaw wiki compile` is run so the decision is in the agent digest by the next spawn.
5. All future tasks inherit this decision as the new truth.

## The two-way chain in plain English

Work flows DOWN (Ari → enforcers → coder) and also UP (coder back → enforcers → Haim if needed). At each rung the target can send it back the way it came. Revisions are expected, not exceptional. What is NOT expected: skipping rungs.

## Who does what — at a glance

| Step | Who acts | Who reviews |
|---|---|---|
| 1 — distribute | Ari | — |
| 2 — domain breakdown | each enforcer (parallel) | — |
| 3 — alignment / escalate | enforcers state positions | Ari packages for Haim if disagreement |
| 4 — collate brief | Ari | — |
| 5 — create tasks | coder | — |
| 6 — task approval | each enforcer | coder revises |
| 6.5 — git + CI prep | Eitan (gate model) or coder (policy model) | — |
| 7 — Haim decides + new truth | Haim | Ari ingests to wiki |

## When this flow is overkill

Trivial tasks — a typo fix, a comment edit, a single-line bug fix with an obvious cause — don't need the full flow. Ari's judgment. Rule of thumb: if the task is contained in a single file, touches no enforcer's domain-critical concerns, and has no parity/money/security implications, the coder can work directly under Idan's minimal review.

## Interaction with other canonical pages

- `concepts/process/milestone-workflow.md` — this page is the zoom-in on step 6 (between spec approved and implementation). Milestone-workflow describes phases; this page describes the kickoff inside the implementation phase.
- `concepts/coding/scope-discipline.md` — once this flow completes and the coder has approved tasks, scope-discipline governs the coding itself.
- `_shared/coordination-rules.md` — the enforcer matrix names WHO reviews what; the escalation ladder defines the try-count rules used in Steps 3 and 6; the bootstrap-vs-steady-state clause explains why this flow isn't yet in full effect during Stage 1.

## Not yet in effect

During Stage 1 (bootstrap seeding), Haim seeds the wiki directly with the admin scribe. This task-kickoff flow activates in Stage 2 onwards, once Ari is the operational Librarian and the first real milestones begin.

```

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
