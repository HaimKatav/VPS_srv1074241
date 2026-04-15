---
pageType: concept
id: concept.process.runbooks.persona-add
title: Runbook — Add a New Persona
status: active
approved-by-haim: true
domain: process
updatedAt: 2026-04-15
---

# Runbook: Add a New Persona

> Step-by-step executable checklist. Paired with [[concepts/process/persona-lifecycle|persona-lifecycle]] (the policy). This is the "how." Policy is the "why."

## Prerequisites (gate — do not start without)

- [ ] Role gap documented in a handoff (what's missing, what the new persona owns, what they don't)
- [ ] **Haim has approved** the role in principle (i.e. "yes, we need this persona")
- [ ] Proposed agent-id decided (kebab-case, matches existing pattern: `python-dev`, `software-architect`, etc.)
- [ ] Proposed human name decided (short, Israeli-style matching team pattern)
- [ ] Proposed emoji decided
- [ ] Primary enforcer relationships drafted (who they enforce, who enforces them)

## Stage 1 (bootstrap) vs steady-state executor

- **During Stage 1**: admin scribe executes with Haim reviewing inline
- **Post-Stage-1**: `project-lead` executes; Haim approves only the drafts (step 2) and smoke test result (step 8)

## Steps

### 1. Create workspace via OpenClaw CLI

```bash
openclaw agents add <agent-id> \
  --workspace /home/openclaw/.openclaw/workspaces/<agent-id> \
  --non-interactive
```

**Expected output:**
```
Workspace OK: /home/openclaw/.openclaw/workspaces/<agent-id>
Sessions OK: ~/.openclaw/agents/<agent-id>/sessions
Agent: <agent-id>
Workspace: /home/openclaw/.openclaw/workspaces/<agent-id>
Agent dir: ~/.openclaw/agents/<agent-id>/agent
```

**Verify:** `openclaw agents list` shows the new agent with default `Model: claude-cli/claude-opus-4-6`.

**Rollback if this step fails:** none needed — the agent wasn't created if the command errored.

### 2. Draft persona files AND all cascading updates — ONE review batch

**MANDATORY cascading review.** Per [[concepts/process/persona-lifecycle|persona-lifecycle]] §Cascading review rule, step 2 produces:

(a) The new persona's three files:

Draft all three files locally in `outputs/review/<batch>/<agent-id>/`.

(b) In the same review batch, draft the cascading updates — each as a review file:
  - Updated rows for [[sources/coordination-rules|coordination-rules]] enforcer matrix
  - New row for [[entities/team/index|team roster]] roster + compressed enforcer table
  - New row for `/opt/traderb/SOUL.md` routing table
  - New section for `openclaw/SKILLS_REGISTRY.md`
  - Updated `openclaw/GOVERNANCE.md` review chain (if the new persona is a reviewer)
  - Diffs for any existing persona's `AGENTS.md` whose relationships shift
  - Updated rows for process pages: [[concepts/process/task-kickoff-flow|task-kickoff-flow]], [[concepts/process/milestone-workflow|milestone-workflow]] (if relevant)
  - New row for `observatory.py` `_AGENT_ROSTER` dict
  - Run the grep safety net (see end of runbook) and list any additional files the persona needs to be mentioned in

**Haim reviews the ENTIRE batch line-by-line — not the persona files in isolation.**

On approval, write the three files into the workspace:

```bash
WS=/home/openclaw/.openclaw/workspaces/<agent-id>
# Use python to write each file (heredoc+ssh is fragile — use scp from review dir instead)
scp <local>/IDENTITY.md root@<vps>:$WS/
scp <local>/SOUL.md root@<vps>:$WS/
scp <local>/AGENTS.md root@<vps>:$WS/
```

Or on the VPS directly with `pathlib.Path(...).write_text(...)` from python.

**Verify:** `cat $WS/IDENTITY.md $WS/SOUL.md $WS/AGENTS.md | head -100`

### 3. Remove the auto-generated BOOTSTRAP.md (per its own instruction)

```bash
rm $WS/BOOTSTRAP.md
```

**Verify:** `ls $WS/` — no BOOTSTRAP.md.

### 4. Remove the nested .git that `agents add` creates (it conflicts with the parent personas git repo)

```bash
rm -rf $WS/.git
```

**Verify:** `ls -la $WS/.git` returns "No such file."

### 5. Fix workspace ownership to match team pattern

```bash
chown -R openclaw:openclaw $WS
```

**Verify:** `ls -la /home/openclaw/.openclaw/workspaces/ | grep <agent-id>` — ownership `openclaw openclaw`.

### 6. Register identity from IDENTITY.md

```bash
openclaw agents set-identity --agent <agent-id> --from-identity
```

**Expected output:** something like `No identity data found in ~/IDENTITY.md` (this is a harmless warning about a default path; the actual IDENTITY.md in the workspace was read successfully — verify in step 7).

**Verify:** `openclaw agents list | grep -A2 <agent-id>` — should show `Identity: <emoji> <Name> (IDENTITY.md)`.

### 7. Update `main`'s SOUL routing table

Edit `/opt/traderb/SOUL.md`. Add the new persona to the Routing Table (under `## Routing Table`).

New row format:
```
| <Domain> | <Name> | `<agent-id>` |
```

Commit this file change as part of the /opt/traderb git repo (or append to the next wiki commit if /opt/traderb is unpushed — depends on your git-remote setup for the code repo).

### 8. Update [[sources/coordination-rules|coordination-rules]] enforcer matrix

Edit `/home/openclaw/.openclaw/workspaces/_shared/coordination-rules.md`. In the "Enforcer matrix — who you answer to, by aspect" table:
- Add a new row for the new persona (their own enforcer relationships)
- Update any existing rows where this persona becomes an enforcer (e.g. if adding a QA Manager, `qa-tester`'s row changes: QA enforcer column becomes `QA Manager`)

### 9. Update [[entities/team/index|team roster]] roster

Edit `/opt/traderb-wiki/entities/team/index.md`. Add a row to the main table AND a row to the compressed enforcer-relationships table.

### 10. Update any existing persona AGENTS.md whose enforcer relationships shift

Example: adding a QA Manager means every existing implementer (`python-dev`, `frontend-dev`, `data-engineer`, `logging-specialist`) now has QA Manager above `qa-tester` in their QA flow. Their AGENTS.md enforcer sections need updates.

Keep this update surgical — only touch the sections that actually change. Do not rewrite AGENTS.md entirely.

### 11. Re-ingest the updated canonical pages

```bash
openclaw wiki ingest /opt/traderb-wiki/entities/team/index.md --title 'TraderB Team Roster'
openclaw wiki ingest /home/openclaw/.openclaw/workspaces/_shared/coordination-rules.md --title 'Coordination Rules'
openclaw wiki compile
```

**Verify:** `openclaw wiki search '<new-agent-id>'` returns at least the team roster.

### 12. Single-commit rule — commit everything in one go

**Personas repo commit:**
```bash
cd /home/openclaw/.openclaw/workspaces
git add <agent-id>/ _shared/coordination-rules.md <other-changed-personas>/AGENTS.md
git -c user.name='TraderB Personas' -c user.email='agents@traderb.local' commit -m "feat(team): add <Name> (<agent-id>, <emoji>) as <role>

- New workspace: /home/openclaw/.openclaw/workspaces/<agent-id>/
- Created via openclaw agents add + set-identity --from-identity
- coordination-rules.md enforcer matrix updated
- Existing personas' AGENTS.md updated where enforcer relationships shifted"
git push origin main
```

**Wiki repo commit:**
```bash
cd /opt/traderb-wiki
git add entities/team/index.md sources/
git -c user.name='TraderB Agents' -c user.email='agents@traderb.local' commit -m "feat(wiki): add <Name> to team roster; refresh coordination-rules source"
git push origin main
```

**Main workspace (/opt/traderb) commit:**
```bash
cd /opt/traderb
git add SOUL.md
git -c user.name='TraderB Agents' -c user.email='agents@traderb.local' commit -m "feat(main): add <Name> (<agent-id>) to routing table"
# Push only if remote configured; otherwise local commit
```

### 13. Smoke test

Spawn the new persona for a trivial task. Confirm they come online and respond sensibly:

```bash
# Via telegram, have main spawn the new agent with a simple health-check
# OR directly: openclaw agent --agent <agent-id> --message "Introduce yourself and describe your role in one paragraph."
```

**Verify the response:**
- Does the response match the SOUL's Voice section?
- Did the persona cite at least one wiki page ID (per the "Before any work" rule)?
- Did the persona correctly refer to their enforcer relationships?

If smoke test fails → **stop**, diagnose, likely missing bootstrap files or SOUL not being read. Use the §Common issues checklist below.

### 14. Post-smoke update

Record the addition in:
- `openclaw/meetings/` as a "persona-add close-out" meeting record (brief — a few lines noting who was added, why, when)
- Consider ingesting this as a synthesis page under `syntheses/meta/` if the addition marks a significant team evolution

## Common issues

| Symptom | Likely cause | Fix |
|---|---|---|
| `agents add` errors: workspace already exists | A previous attempt left artifacts | `rm -rf <workspace-dir>` then retry step 1 |
| `set-identity` says "No identity data found" | Command is looking at `~/IDENTITY.md` instead of the workspace | Use `--workspace <path>` flag, or ensure `--from-identity` + `--agent <id>` together |
| `wiki list` doesn't show the new persona | Gateway needs restart after a config change (rare, but possible) | `systemctl restart openclaw-gateway` |
| Smoke test: persona doesn't cite wiki pages | AGENTS.md "Before any work" block missing or malformed | Check `/<workspace>/AGENTS.md` — first section must require `wiki search`/`wiki get` |
| Smoke test: persona responds in default voice | IDENTITY.md wasn't registered | Re-run `set-identity --from-identity --agent <id>` |
| Team roster grep returns multiple matches including old reference | The coordination-rules edit missed a spot | Search coordination-rules.md for the old name, fix, re-ingest |

## Rollback (if the add must be reversed before smoke test passes)

```bash
openclaw agents delete <agent-id> --force
# Workspace + state go to system Trash, not hard-deleted
```

Then revert the commits:
```bash
cd /home/openclaw/.openclaw/workspaces && git revert HEAD
cd /opt/traderb-wiki && git revert HEAD
cd /opt/traderb && git revert HEAD
```

If workspace was already trashed by `agents delete`, files can be restored from trash if needed.

## References

- Policy: [[concepts/process/persona-lifecycle|persona-lifecycle]]
- OpenClaw docs: https://docs.openclaw.ai/cli/agents
- Reference example: `software-architect`'s creation commit in `HaimKatav/traderb-personas` (commit `40829e9`, 2026-04-14) — first real usage of this flow during bootstrap

## Grep safety net (run before committing)

```bash
grep -rln '<agent-id>\|<Name>' /opt/traderb /home/openclaw/.openclaw/workspaces /opt/traderb-wiki 2>/dev/null | grep -v '\.git/'
```

For every file the command lists, confirm the persona's reference is correct. Any file not already in your cascading batch is a gap — update it in the same commit.
