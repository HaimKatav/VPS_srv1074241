---
pageType: concept
id: concept.process.persona-lifecycle
title: Persona Lifecycle — Adding, Removing, Adapting Agents
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Persona Lifecycle

> Defines how the agent roster changes over time: adding a new persona, removing one, or adapting an existing role. Every lifecycle event has a defined doc-update chain so the wiki stays coherent with reality.

## Why this exists

The team is not static. New roles emerge as scope grows (e.g. Idan was added when software architecture separated from project-lead). Roles get refined as we learn what's needed. Occasionally a persona is retired if its function is absorbed elsewhere. Without a documented process, these changes cause drift: `main`'s routing table disagrees with the team roster, which disagrees with coordination-rules, which disagrees with the actual operating persona files. Drift makes agents cite outdated relationships. Drift kills coherence.

## Three lifecycle events

### 1. Adding a persona

**Trigger:** a role gap is identified — by Haim directly, by Ari observing friction in the existing team, or surfaced via retrospectives.

**Approval bar:** Haim only. Adding an agent is a team-composition decision.

**Process:**
1. **Gap identified** → handoff to Ari describing the gap and proposed role.
2. **Haim approves the role in principle** (does this role need to exist?).
3. **Research + draft.** Whoever is executing (Ari post-Stage-1, admin during Stage 1) researches the role (look at similar-scale teams, what the role owns in analogous systems) and drafts three files: IDENTITY.md, SOUL.md, AGENTS.md. Draft also proposes the persona's name + emoji.
4. **Haim reviews the drafts** line-by-line. Edits/approves.
5. **Execute via OpenClaw CLI:**
   - `openclaw agents add <agent-id> --workspace /home/openclaw/.openclaw/workspaces/<agent-id> --non-interactive`
   - Write the approved IDENTITY.md, SOUL.md, AGENTS.md into the workspace
   - Remove the default BOOTSTRAP.md (per its own instruction)
   - Fix workspace ownership to `openclaw:openclaw` (matches team pattern)
   - `openclaw agents set-identity --agent <agent-id> --from-identity` (reads IDENTITY.md)
   - Verify: `openclaw agents list` shows the new persona
6. **Doc-update chain (in order, single commit):**
   - Update `main`'s SOUL routing table (new row pointing to the new agent)
   - Update `_shared/coordination-rules.md` enforcer matrix (new persona's row + any relationships that shift elsewhere)
   - Update `entities/team/index.md` roster (new row)
   - Update any existing `AGENTS.md` whose enforcer relationships now include the new persona
7. **Commit + push:**
   - Personas repo (`HaimKatav/traderb-personas`): all SOUL/AGENTS/IDENTITY changes
   - Wiki repo (`HaimKatav/VPS_srv1074241`): updated `coordination-rules.md` + `entities/team/index.md` (re-ingested as sources)
8. **Smoke test:** Ari spawns the new persona for a trivial task to confirm they come online, read their AGENTS.md, query the wiki, and respond sensibly.

### 2. Removing a persona

**Trigger:** role is no longer needed (function absorbed elsewhere, scope shift, deprecation of an entire subsystem).

**Approval bar:** Haim only. Accountability doesn't vanish silently.

**Process:**
1. **Proposal to Ari** with: why the role is no longer needed, where in-flight work goes, what relationships will shift.
2. **Haim reviews and approves.**
3. **Reassign in-flight work.** Every task currently routed to the persona gets reassigned (to whoever absorbs the function). Nothing in limbo.
4. **Wind down active sessions.** Check no active session is mid-work for the persona.
5. **Archive the workspace — do NOT delete.** Move `/home/openclaw/.openclaw/workspaces/<agent-id>/` to `/home/openclaw/.openclaw/workspaces/_archive/<agent-id>_<YYYY-MM-DD>/`. History is kept.
6. **Execute via OpenClaw CLI:** `openclaw agents delete <agent-id>` (removes routing + state; workspace already archived).
7. **Doc-update chain (in order, single commit):**
   - Update `main`'s SOUL routing table (remove row; update any "absorbed-by" notes if relevant)
   - Update `_shared/coordination-rules.md` enforcer matrix (remove row + reassign any relationships the persona was in)
   - Update `entities/team/index.md` (move to an "Archived" section at bottom — keep for history, annotate with date + absorbed-by)
   - Update any existing `AGENTS.md` whose enforcer relationships referenced the removed persona
8. **Commit + push** personas repo + wiki repo.
9. **Milestone-close entry** in `syntheses/meta/` or similar noting the retirement, what was absorbed where, why.

### 3. Adapting a persona

**Trigger:** role drift (persona's real behavior has evolved past their docs), scope expansion (new responsibilities), scope contraction (something moved to another persona), relationship shift (new enforcer pairing).

**Approval bar depends on change type:**

| Change type | Approval required |
|---|---|
| **Material** — new responsibilities, removed responsibilities, changed enforcer relationships, change to who they report to | **Haim** |
| **Minor** — wording clarification, skill list refresh, tone refinement, add/remove a file reference | **Ari** |

**Process (material changes):**
1. **Proposal to Ari** — what's changing, why, what the new SOUL/AGENTS should say, which other pages will need updates.
2. **Ari drafts the updated SOUL/AGENTS** (or has the persona involved in their own proposal).
3. **Haim reviews line-by-line.** Approves or edits.
4. **Apply updates:**
   - Write new `SOUL.md` and/or `AGENTS.md` in the persona's workspace
   - If IDENTITY changed, run `openclaw agents set-identity --agent <agent-id> --from-identity`
5. **Doc-update chain (single commit):**
   - If enforcer relationships shifted: update `_shared/coordination-rules.md`
   - If role description changed: update `entities/team/index.md`
   - If the shift affects other personas' enforcer relationships: update their `AGENTS.md` too
6. **Commit + push** personas + wiki.

**Process (minor changes):** Ari edits directly, commits to personas repo, pushes. No Haim approval needed. If in doubt whether a change is minor, treat as material.

## The single-commit rule

This is the hard rule. **Whenever a persona lifecycle event touches more than one file, all touched files land in the same commit.** Never commit a routing-table update without the matching roster and coordination-rules updates. Never update a persona's AGENTS.md without updating the pages that reference them.

If the single-commit rule is broken mid-flight, Ari must note the gap immediately and close it in the next commit. Drift detected in a lint run surfaces to Haim.


## Cascading review rule (named principle)

When a persona lifecycle event is proposed, **every downstream document change triggered by that event must be drafted IN THE SAME REVIEW BATCH** as the persona's own IDENTITY/SOUL/AGENTS files. Haim reviews them together, not serially. This is how we prevent drift — and it's how we caught the Guy-add dogfood gap on 2026-04-15, which led to this rule being named.

### Why
Approving a persona's SOUL without simultaneously reviewing the ripple updates (coordination-rules, team roster, other personas' AGENTS, routing table, SKILLS_REGISTRY, etc.) means the approver cannot judge coherence. They see a character card without seeing the team as it will be on the morning after the commit.

### What "cascading" covers

For EVERY add / remove / material-adapt event, the drafter produces a single review batch containing:

1. The persona's own files — IDENTITY.md, SOUL.md, AGENTS.md (for add; edits of existing for adapt; empty/archive metadata for remove).
2. `_shared/coordination-rules.md` — enforcer matrix updates.
3. `entities/team/index.md` — roster update.
4. `/opt/traderb/SOUL.md` — main router routing table.
5. `openclaw/SKILLS_REGISTRY.md` — new/edited persona section.
6. `openclaw/GOVERNANCE.md` — review chain table, if the persona is a reviewer.
7. Any existing persona's `AGENTS.md` whose enforcer relationships shift — one or more files.
8. Any process page that names specific enforcers — `concepts/process/task-kickoff-flow.md`, `concepts/process/milestone-workflow.md`, scope-discipline.md — if the persona's role changes those flows.
9. `observatory.py` static roster dict — add/remove/update the persona entry; update comment if deprecating a referenced file.
10. Any active handoffs that must be reassigned (remove event only).

### Grep safety net

Before committing ANY lifecycle event, run this command and update every file it lists that references the changing persona:

```bash
grep -rln '<agent-id>\|<Name>' /opt/traderb /home/openclaw/.openclaw/workspaces /opt/traderb-wiki 2>/dev/null | grep -v '\.git/'
```

Any file in the output that contains stale or obsolete language about the persona must be updated in the same commit. No stragglers.

### Stage 1 note

During bootstrap seeding (Stage 1 now), Haim + admin execute lifecycle events directly and drafters present cascading review batches. The Guy-add commit (scheduled 2026-04-15) is the first fully-batched reference example.

## Wiki update procedure — specific file paths

For every lifecycle event, the files touched are predictable. Codified here so Ari (or admin during Stage 1) doesn't have to rederive:

```
Personas repo (/home/openclaw/.openclaw/workspaces/):
- <agent-id>/IDENTITY.md       (add: write; adapt-material: edit; remove: archive then delete)
- <agent-id>/SOUL.md           (add: write; adapt-material: edit; remove: archive)
- <agent-id>/AGENTS.md         (add: write; adapt: edit; remove: archive)
- other-agent/AGENTS.md        (edit if this persona's enforcer relationship shifts toward them)

Wiki repo (/opt/traderb-wiki/):
- entities/team/index.md                (always update)
- concepts/process/task-kickoff-flow.md (update Step 2 enforcer list if relevant)
- concepts/process/milestone-workflow.md(update if persona-specific phase language)

Personas repo (_shared/):
- _shared/coordination-rules.md         (update enforcer matrix — nearly always)

Main workspace (/opt/traderb/):
- SOUL.md                              (update main router's routing table)
- AGENTS.md                            (update if references main's routing)
- openclaw/SKILLS_REGISTRY.md          (new/edited persona section — always)
- openclaw/GOVERNANCE.md               (review chain table — if reviewer role)
- observatory.py                       (static _AGENT_ROSTER dict — always)

Ingest step (wiki):
- openclaw wiki ingest /opt/traderb-wiki/entities/team/index.md --title 'TraderB Team Roster'
- openclaw wiki ingest /home/openclaw/.openclaw/workspaces/_shared/coordination-rules.md --title 'Coordination Rules'
- openclaw wiki compile
```

All ingest steps refresh the source projections so agents querying via `wiki_search` see the updated reality.

## Who owns this process

- **Haim** — approval authority for add, remove, and material adapt.
- **Ari (post-Stage-1)** — owns the execution: CLI commands, file edits, doc-update chain, ingestions, commits. Also owns minor adapt changes without Haim gate.
- **Admin scribe (during Stage 1)** — stands in for Ari until the Stage 1 close-out transition.
- **The persona itself** — may propose their own adapt (e.g. Lior notices his AGENTS.md is missing a context reference). Goes through Ari.

## Stage 1 caveat

During bootstrap seeding (Stage 1), Haim + admin directly execute lifecycle events. Idan's creation this session is the reference example — documented in the personas repo commit history. Post-Stage-1, Ari takes over execution.

## Related

- `_shared/coordination-rules.md` — enforcer matrix that must stay in sync with roster
- `entities/team/index.md` — the canonical roster that must stay in sync with reality
- `concepts/meta/wiki-information-architecture.md` — where page types live
- `concepts/process/milestone-workflow.md` — how tasks flow (this page is its team-composition counterpart)

## What this prevents

- `main` routing table pointing to a persona who doesn't exist anymore
- Team roster listing a persona with old responsibilities
- `coordination-rules.md` enforcer matrix referencing a persona whose relationships shifted
- A new persona added without their enforcer relationships propagated into other personas' AGENTS.md
- Silent role drift where a persona's real behavior evolves past their documented SOUL
