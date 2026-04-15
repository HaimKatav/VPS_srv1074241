---
pageType: concept
id: concept.process.runbooks.persona-remove
title: Runbook — Remove a Persona
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Runbook: Remove a Persona

> Executable checklist for retiring a persona. Paired with `concepts/process/persona-lifecycle.md` (the policy).

## Prerequisites (gate — do not start without)

- [ ] Written proposal documenting: why this persona is no longer needed, where each of their responsibilities is being absorbed, what relationships will shift
- [ ] **Haim has approved removal** — this is a team-composition change with accountability implications; never remove without explicit sign-off
- [ ] Absorbing persona(s) identified for every current responsibility
- [ ] In-flight work inventoried — list every open task routed to this persona
- [ ] Archive path decided: `/home/openclaw/.openclaw/workspaces/_archive/<agent-id>_<YYYY-MM-DD>/`

## Steps

### 1. Reassign in-flight work

For every open task, handoff, or sprint ticket currently routed to the persona:
- Update the task assignee to the absorbing persona
- Notify the absorbing persona via handoff (task brief + any context they need)
- Mark the reassignment in the task comments for audit

**Verify:** `openclaw/handoffs/` search for the departing agent-id returns zero active threads.

### 2. Wind down any active sessions

Check for active sessions:
```bash
openclaw agents list
# Note any "active" indicators for this persona
```

If there's an active session mid-work, DO NOT delete. Wait for it to reach a natural checkpoint, then archive its transcript.

### 3. Archive the workspace before deletion

```bash
AGENT_ID=<agent-id>
DATE=$(date +%Y-%m-%d)
ARCHIVE_DIR=/home/openclaw/.openclaw/workspaces/_archive/${AGENT_ID}_${DATE}
mkdir -p /home/openclaw/.openclaw/workspaces/_archive
cp -r /home/openclaw/.openclaw/workspaces/${AGENT_ID} ${ARCHIVE_DIR}
```

**Verify:** `ls -la ${ARCHIVE_DIR}` — full copy of SOUL/IDENTITY/AGENTS + any memory files + workspace history.

Archive lives in the personas git repo forever — easy to resurrect or audit later.

### 4. Delete via OpenClaw CLI

```bash
openclaw agents delete <agent-id> --force
```

**Expected output:** confirmation of deletion.

Per docs: workspace, agent state, and session transcript directories are moved to system Trash (not hard-deleted). Combined with our archive in step 3, we have two preservation paths.

**Verify:** `openclaw agents list` no longer shows the persona.

### 5. Update `main`'s SOUL routing table

Edit `/opt/traderb/SOUL.md`. Remove the persona's row from the Routing Table. Optionally, add an "absorbed by" note in the commit message (not in the routing table — that just gets noisy over time).

### 6. Update `_shared/coordination-rules.md` enforcer matrix

Edit the enforcer matrix:
- Remove the persona's own row
- For every other persona whose enforcer relationships named this persona, update those rows to point to the absorbing persona instead

### 7. Update `entities/team/index.md` roster

Edit the main roster table — **move the row to an "Archived personas" section at the bottom** (do not just delete; keep for history).

Format for archived row:
```
| <old-agent-id> | <Name> | <emoji> | archived <YYYY-MM-DD> | <one-line reason> | absorbed by <new-persona> |
```

Also remove from the compressed enforcer-relationships table.

### 8. Update existing persona AGENTS.md files whose enforcer relationships named the removed persona

Search all persona AGENTS.md for the old agent-id or name:
```bash
grep -rln "<old-agent-id>\|<Old Name>" /home/openclaw/.openclaw/workspaces/
```

For each match, update the enforcer reference to point to the absorbing persona.

### 9. Re-ingest the updated canonical pages

```bash
openclaw wiki ingest /opt/traderb-wiki/entities/team/index.md --title 'TraderB Team Roster'
openclaw wiki ingest /home/openclaw/.openclaw/workspaces/_shared/coordination-rules.md --title 'Coordination Rules'
openclaw wiki compile
```

### 10. Write a milestone-close synthesis entry

Create `syntheses/meta/persona-retirement_<YYYY-MM-DD>_<agent-id>.md` with:
- Who was retired, when, why
- What responsibilities went where
- Link to the archive path
- Any retrospective lessons

Ingest as a source for future searchability.

### 11. Single-commit rule — commit all changes together

**Personas repo:**
```bash
cd /home/openclaw/.openclaw/workspaces
git add _archive/<agent-id>_<date>/ _shared/coordination-rules.md <changed-personas>/AGENTS.md
# Note: <agent-id>/ directory is already deleted by `agents delete` — git sees that
git -c user.name='TraderB Personas' -c user.email='agents@traderb.local' commit -m "feat(team): retire <Name> (<agent-id>); responsibilities absorbed by <new-persona>

- Workspace archived to _archive/<agent-id>_<date>/
- openclaw agents delete executed (force, workspace to system trash)
- coordination-rules.md enforcer matrix updated
- Existing personas' AGENTS.md updated where relationships referenced <old>"
git push origin main
```

**Wiki repo:**
```bash
cd /opt/traderb-wiki
git add entities/team/index.md syntheses/meta/persona-retirement_*.md sources/
git -c user.name='TraderB Agents' -c user.email='agents@traderb.local' commit -m "feat(wiki): retire <Name> from team roster; add retirement synthesis"
git push origin main
```

**Main workspace (/opt/traderb):**
```bash
cd /opt/traderb
git add SOUL.md
git -c user.name='TraderB Agents' -c user.email='agents@traderb.local' commit -m "feat(main): remove <agent-id> from routing table — absorbed by <new>"
```

### 12. Verification

- [ ] `openclaw agents list` — old persona gone
- [ ] `openclaw wiki search <old-agent-id>` — only returns the archive/retirement entries, not active references
- [ ] Grep persona workspaces for old name — only the _archive/ copy matches
- [ ] Spawn the absorbing persona for a task that used to go to the removed persona — they handle it correctly

## Rollback (if something goes wrong partway)

Restore from the archive you made in Step 3:

```bash
# Restore workspace
cp -r /home/openclaw/.openclaw/workspaces/_archive/${AGENT_ID}_${DATE} /home/openclaw/.openclaw/workspaces/${AGENT_ID}
chown -R openclaw:openclaw /home/openclaw/.openclaw/workspaces/${AGENT_ID}

# Re-register with OpenClaw
openclaw agents add <agent-id> --workspace /home/openclaw/.openclaw/workspaces/<agent-id> --non-interactive
openclaw agents set-identity --agent <agent-id> --from-identity

# Revert doc commits (most recent first)
cd /opt/traderb && git revert HEAD
cd /opt/traderb-wiki && git revert HEAD
cd /home/openclaw/.openclaw/workspaces && git revert HEAD
```

Or restore from system trash if the archive somehow got skipped.

## Common issues

| Symptom | Likely cause | Fix |
|---|---|---|
| `agents delete` asks for confirmation in non-interactive context | Missing `--force` flag | Add `--force` |
| Old persona still appears in agent digest after compile | Plugin cache stale | Run `openclaw wiki compile` again; restart gateway if persistent |
| Grep finds references to old persona in some AGENTS.md | Step 8 missed a file | Search exhaustively: `grep -rln '<old-name>' /home/openclaw/.openclaw/workspaces/` |

## References

- Policy: `concepts/process/persona-lifecycle.md`
- OpenClaw docs: https://docs.openclaw.ai/cli/agents
- Related: `concepts/process/runbooks/persona-add.md`, `concepts/process/runbooks/persona-adapt.md`

## Cascading review batch (mandatory)

Per `concepts/process/persona-lifecycle.md` §Cascading review rule, removal produces a single review batch BEFORE execution, covering:

- Updated rows/removals in `_shared/coordination-rules.md` enforcer matrix (every row that references the departing persona)
- Updated `entities/team/index.md` — move persona row to "Archived" section; update compressed enforcer table
- Updated `/opt/traderb/SOUL.md` routing table (remove row)
- Updated `openclaw/SKILLS_REGISTRY.md` (remove section OR move to archive)
- Updated `openclaw/GOVERNANCE.md` review chain (reassign any reviewer role)
- Diffs for every other persona's `AGENTS.md` where the departing persona was named
- Updated process pages (`task-kickoff-flow.md`, `milestone-workflow.md`) if they named the persona
- Updated `observatory.py` `_AGENT_ROSTER` (remove entry)
- Retirement synthesis page (`syntheses/meta/persona-retirement_<date>_<agent-id>.md`)

Haim reviews the batch before any OpenClaw CLI command runs.

## Grep safety net (run before committing)

```bash
grep -rln '<old-agent-id>\|<Old Name>' /opt/traderb /home/openclaw/.openclaw/workspaces /opt/traderb-wiki 2>/dev/null | grep -v '\.git/'
```

Every hit that still uses active language (not historical/archive context) must be updated in the same commit. Archive references in `_archive/` and retirement synthesis pages are expected to still name the persona — those are OK to leave.
