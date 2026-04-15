---
pageType: source
id: source.runbook-adapt-a-persona
title: "Runbook: Adapt a Persona"
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/process/runbooks/persona-adapt.md
ingestedAt: 2026-04-15T13:03:28.005Z
updatedAt: 2026-04-15T13:03:28.005Z
status: active
---

# Runbook: Adapt a Persona

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/process/runbooks/persona-adapt.md`
- Bytes: 7766
- Updated: 2026-04-15T13:03:28.005Z

## Content
````text
---
pageType: concept
id: concept.process.runbooks.persona-adapt
title: Runbook — Adapt an Existing Persona
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Runbook: Adapt an Existing Persona

> Executable checklist for updating a persona's SOUL, AGENTS.md, role scope, or enforcer relationships. Paired with `concepts/process/persona-lifecycle.md` (the policy).

## Two paths — material vs minor

Before starting, classify the change. This determines the approval bar and the documentation chain.

### Material change (Haim approves)
- New responsibilities added
- Existing responsibilities removed
- Enforcer relationships changed (who they answer to, who answers to them)
- Reporting chain shifts
- Model change (e.g. promoting a persona from Sonnet to Opus)
- Any change that affects how OTHER personas interact with this one

### Minor change (Ari's judgment, no Haim gate)
- Wording clarifications in SOUL or AGENTS
- Skill list refresh (add/remove a skill reference)
- Tone refinement
- Adding a file reference or wiki page citation
- Fixing a typo, broken link, or stale date

**If in doubt, treat as material.** Haim-ticks are cheap; undetected drift isn't.

---

## Material change path

### Prerequisites

- [ ] Written proposal of the change (what's different, why, which other pages/personas it affects)
- [ ] **Haim has approved the change in principle**
- [ ] Updated SOUL/AGENTS drafts prepared (in `outputs/review/` or equivalent)
- [ ] Cross-impact list: which other personas' AGENTS.md or which canonical wiki pages will need updates in the same commit

### Steps

#### 1. Haim reviews drafts line by line

Present the before/after in outputs/review/. Haim approves, edits, or rejects.

#### 2. Apply SOUL/AGENTS updates to the workspace

```bash
WS=/home/openclaw/.openclaw/workspaces/<agent-id>
# Write the new content — use scp from your review dir or direct python write
scp <local>/SOUL.md root@<vps>:$WS/SOUL.md
scp <local>/AGENTS.md root@<vps>:$WS/AGENTS.md
# Preserve ownership
ssh root@<vps> "chown openclaw:openclaw $WS/SOUL.md $WS/AGENTS.md"
```

**Verify:** `cat $WS/SOUL.md $WS/AGENTS.md | head -40` matches the approved draft.

#### 3. If IDENTITY changed (name, emoji, theme), re-register

```bash
openclaw agents set-identity --agent <agent-id> --from-identity
```

**Verify:** `openclaw agents list | grep -A2 <agent-id>` shows the new identity.

#### 4. Update `_shared/coordination-rules.md` enforcer matrix (if relationships shifted)

Every enforcer-relationship row that involves this persona — on either side — must match the new reality.

#### 5. Update `entities/team/index.md` roster

Update the persona's row to reflect:
- New one-line role description (if changed)
- New primary enforcer relationships (if shifted)

Also update the compressed enforcer-relationships table.

#### 6. Update other personas' AGENTS.md whose relationships shifted

If this adaptation changes how this persona interacts with another, that OTHER persona's AGENTS.md needs an update too. Common cases:
- This persona became an enforcer for another — update the other's "who enforces me" section
- This persona was freed from enforcing another — remove the obsolete reference

Grep search for sanity:
```bash
grep -rln "<agent-id>\|<Name>" /home/openclaw/.openclaw/workspaces/
```

Review each hit. Update where the semantics changed.

#### 7. If the change affects process flows, update the relevant wiki concept/process pages

Examples:
- If adaptation changes the task-kickoff flow → update `concepts/process/task-kickoff-flow.md`
- If adaptation changes the QA bug loop → update `_shared/coordination-rules.md`
- If adaptation introduces new review responsibilities → update `concepts/coding/scope-discipline.md`

Each such update also gets re-ingested.

#### 8. Re-ingest all touched canonical pages

```bash
openclaw wiki ingest /opt/traderb-wiki/entities/team/index.md --title 'TraderB Team Roster'
openclaw wiki ingest /home/openclaw/.openclaw/workspaces/_shared/coordination-rules.md --title 'Coordination Rules'
# Plus any other concept/process pages you touched
openclaw wiki compile
```

#### 9. Single-commit rule — commit all changes together

**Personas repo:**
```bash
cd /home/openclaw/.openclaw/workspaces
git add <agent-id>/ _shared/coordination-rules.md <other-personas>/AGENTS.md
git -c user.name='TraderB Personas' -c user.email='agents@traderb.local' commit -m "refactor(team): adapt <Name> (<agent-id>) — <one-line summary>

- SOUL.md updated: <what changed>
- AGENTS.md updated: <what changed>
- coordination-rules.md: <what shifted>
- Other personas updated: <list>"
git push origin main
```

**Wiki repo:**
```bash
cd /opt/traderb-wiki
git add entities/team/index.md sources/ <any-other-concept-pages>
git -c user.name='TraderB Agents' -c user.email='agents@traderb.local' commit -m "refactor(wiki): reflect <Name> adaptation — <summary>"
git push origin main
```

#### 10. Verification

- [ ] Spawn the persona for a task matching the new scope. Confirm they behave per the updated SOUL.
- [ ] Spawn a neighbor persona (one whose AGENTS.md was updated) and confirm they understand the new relationship.
- [ ] `openclaw wiki search <key new term from the adapted SOUL>` returns the updated source.

---

## Minor change path (no Haim gate)

### Prerequisites

- [ ] Change is genuinely minor by the definition above
- [ ] Ari (or admin during Stage 1) has inspected the change and agrees it's minor

### Steps

#### 1. Apply the edit directly to the workspace file

```bash
scp <fixed-file> root@<vps>:/home/openclaw/.openclaw/workspaces/<agent-id>/<file>
ssh root@<vps> "chown openclaw:openclaw /home/openclaw/.openclaw/workspaces/<agent-id>/<file>"
```

#### 2. If the edit affected how other agents see this persona (rare for minor changes), update the same targets as material step 6

#### 3. Commit to personas repo

```bash
cd /home/openclaw/.openclaw/workspaces
git add <agent-id>/<file>
git -c user.name='TraderB Personas' -c user.email='agents@traderb.local' commit -m "chore(team): minor edit to <Name>'s <file> — <one-line summary>"
git push origin main
```

#### 4. If the edit was a skill reference or file path, re-ingest if the persona's SOUL or AGENTS is ingested (we don't currently ingest all persona files — check first)

#### 5. No smoke test required for minor edits; next natural task spawn validates

---

## When to escalate from minor → material mid-flight

If while making what you thought was a minor edit you realize it actually changes a relationship or responsibility:

1. STOP
2. Revert the edit if already committed: `cd .../workspaces && git revert HEAD`
3. Write up the proposal properly
4. Submit for Haim review via the material path

This reclassification is normal — err on the side of caution.

## Common issues

| Symptom | Likely cause | Fix |
|---|---|---|
| Persona's behavior didn't change after adaptation | They're in a cached session | Spawn a fresh session; old sessions use the SOUL they were born with |
| Grep finds old name/reference after adaptation | Missed a file in step 6 | Full re-grep across all workspace AGENTS.md; update + commit supplementary |
| Coordination-rules source out of sync with persona file | Forgot to re-ingest | Re-run `openclaw wiki ingest` on the updated file + `wiki compile` |
| Smoke test: persona doesn't cite the new reference | Persona only sees digest from last compile | Run `openclaw wiki compile` + restart gateway if persistent |

## References

- Policy: `concepts/process/persona-lifecycle.md`
- Related runbooks: `concepts/process/runbooks/persona-add.md`, `concepts/process/runbooks/persona-remove.md`
- OpenClaw docs: https://docs.openclaw.ai/cli/agents

````

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
