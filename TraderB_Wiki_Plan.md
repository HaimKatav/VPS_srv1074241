# TraderB × memory-wiki — Execution Plan

**Owner:** Haim
**Status:** Draft v2 — **switched from Ar9av/obsidian-wiki to OpenClaw's native `memory-wiki` plugin** after docs review
**Last updated:** 2026-04-14

---

## Context (for future-me picking this up cold)

### What TraderB is
Autonomous, learning algo-trading bot for NQ futures (Nasdaq-100, CME). Target: pass Topstep $50k Combine, then scale on Apex Trader Funding. Core loop: backtest → live → LLM analysis of metrics → strategy refinement → repeat. **Parity is sacred** — backtest and live tick paths must be bit-identical. No black boxes.

Phases: (1) MVP on Tradovate paper → (2) strategy iteration via LLM-on-metrics → (3) multi-account scaling → (4) proprietary data/models + pro-trader MCP feedback loop.

### The VPS setup already in place
- Host: `srv1074241.hstgr.cloud` / `traderb.duckdns.org` (root SSH).
- Code tree: `/opt/traderb/` — bot, docs, suites, runs, skills. Includes `SOUL.md`, `IDENTITY.md`, `AGENTS.md`, `PROJECT_VISION.md`, `GOVERNANCE.md`, `SKILLS_REGISTRY.md`, `SELF_IMPROVEMENT.md`, `memory/` (timestamped dumps), `decisions/`, `handoffs/`, `meetings/`.
- **OpenClaw runtime** (v2026.4.10): one `main` router + 13 personas (Ari project-lead, Noa strategy-architect, Lior python-dev, Maya frontend-dev, Dan data-engineer, Yael QA, Eitan devops, Tamar security, Omer risk, Shira compliance, Gal logging, Rotem UX, Amit transparency). Each has its own workspace.
- **Routing**: Telegram → `main` → `sessions_spawn` → persona. Coordination via file handoffs in `openclaw/handoffs/`.
- **Vikunja** at `/pm/` is single source of truth for tasks (Haim = user ID 2). Project Lead owns it.
- **nginx** reverse proxy, Let's Encrypt, UFW 22/80/443/5678 open. Services bind to 127.0.0.1.
- **Cost rule**: Gemini Flash for sub-agent spawns, Sonnet for coding, Opus only for strategy/risk/compliance.
- Known issue: Claude Max quota shared between Cowork + VPS agents.

### The diagnosis
Prior dev with Claude Code + Cowork produced inconsistent results, baked fake data, and required manual architecture fixes. Root cause: grounding failure — no shared brain across sessions. `memory/` dumps and the self-improving skill try to fix it but are unstructured logs, not retrievable knowledge.

### The tool: OpenClaw's native memory-wiki plugin (bundled)
- **Docs**: `https://docs.openclaw.ai/plugins/memory-wiki`, `https://docs.openclaw.ai/cli/wiki`, `https://docs.openclaw.ai/concepts/memory`.
- Compiles durable memory into a "provenance-rich knowledge vault." **Does NOT replace** the active memory plugin (memory-core) — it adds a structured knowledge layer beside it. `memory-core` still owns recall/promotion/dreaming.
- Storage layout (created by `openclaw wiki init`):
  ```
  <vault>/entities/, concepts/, syntheses/, sources/, reports/
  <vault>/_attachments/, _views/, .openclaw-wiki/
  <vault>/index.md, inbox.md, AGENTS.md, WIKI.md
  ```
- Native provenance: structured claims + evidence, confidence tracking, contradiction + freshness detection, compiled digests.
- Agent tools: `wiki_search`, `wiki_get`, `wiki_apply`, `wiki_lint`.
- CLI: `openclaw wiki init | status | doctor | ingest | compile | lint | search | get | apply | bridge import | unsafe-local import | obsidian ...`.
- **Obsidian mode** via `vault.renderMode: obsidian`. Writes Obsidian-friendly markdown. Optional official Obsidian CLI for `obsidian status | search | open | command | daily`.
- Config goes under `plugins.entries.memory-wiki.config` via `openclaw config set`.

### Why memory-wiki over Ar9av/obsidian-wiki (earlier candidate)
- Native → skills actually load; `openclaw wiki` CLI works; agent tools are first-class.
- Auto-bridges from OpenClaw memory — our existing `memory/` dumps feed it via `bridge import`.
- Built-in provenance + contradiction tracking matches our "no black box" rule out of the box.
- Compiled digests give agents cheap, structured context (aligned with cost rule).

### Laptop access decision
**Syncthing** (VPS ↔ laptop peer sync, no cloud middleman) for full read+write from Obsidian. Already installed on VPS (device ID `6US342C-C6ZDUVH-5UXKEFC-BTJAAYA-OWPKVLV-S5TTR7E-4CVWMYH-AMSQNQP`). Requires UFW port 22000/tcp. Plus optional nginx read-view at `/wiki/` (deferred to Stage 4-ish once there's content).

### False start to roll back
Earlier this session I installed `Ar9av/obsidian-wiki` (GitHub) via its `setup.sh` and hand-scaffolded `/opt/traderb-wiki/` with `_raw/`, `_meta/taxonomy.md`, `.manifest.json`. Both are being replaced by the memory-wiki native approach.

---

## Guiding Model (unchanged)

Three artifacts, three roles.
- **Wiki = the brain.** `/opt/traderb-wiki/` (OpenClaw-managed vault).
- **Code = the body.** `/opt/traderb/`.
- **Vikunja = the calendar.** `/pm/`.

## Two Non-Negotiable Rules

1. **Wiki-first.** No code change without a wiki page describing the why. To deviate: update the page → mark status `proposed` → Haim approves → touch code.
2. **Provenance + confidence = trust.** memory-wiki tracks confidence and provenance natively. Add one custom marker: trading rules and prop firm rules must carry `approved-by-haim: true` in their page frontmatter. Agents may read lower-confidence pages for context but never base a trading decision on one.

---

## Stage 0 — Stand up the vault via OpenClaw native (1–2 days, no bot work)

**Rollback first.**
- [ ] Remove symlinks installed by `Ar9av/obsidian-wiki` setup.sh from `~/.claude/skills/`, `~/.agents/skills/`, `~/.gemini/antigravity/skills/`, `~/.codex/skills/`, and any project-local `.claude|.cursor|.windsurf|.agents/skills/` pointing back to `/opt/obsidian-wiki/.skills/*`.
- [ ] Delete `/opt/obsidian-wiki/` source tree.
- [ ] Empty `/opt/traderb-wiki/` (reset with `git rm -rf`, keep git history of the false start for audit).

**Configure + initialize memory-wiki.**
- [ ] `openclaw config get plugins.entries.memory-wiki` — see current state.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.enabled true`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.vaultMode isolated`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.vault.path /opt/traderb-wiki`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.vault.renderMode obsidian`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.obsidian.enabled true`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.obsidian.useOfficialCli true`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.bridge.enabled true` (pull from memory-core).
- [ ] `openclaw config set plugins.entries.memory-wiki.config.bridge.readMemoryArtifacts true`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.bridge.followMemoryEvents true`.
- [ ] `openclaw config set plugins.entries.memory-wiki.config.context.includeCompiledDigestPrompt true`.
- [ ] `openclaw config validate` — make sure config is clean before restart.
- [ ] Restart gateway: `systemctl restart openclaw-gateway` (or whatever the unit is — check `openclaw doctor`).
- [ ] Install official Obsidian CLI on VPS: `npm install -g obsidian-cli` (or per docs).
- [ ] `openclaw wiki status` — verify plugin active.
- [ ] `openclaw wiki init` — scaffold vault (entities/, concepts/, syntheses/, sources/, reports/, etc.).
- [ ] `openclaw wiki doctor` — confirm green.

**Transport to laptop.**
- [x] Syncthing installed (apt), folder `traderb-wiki` points at `/opt/traderb-wiki`, GUI user `haim` + strong pass (stored at `/root/.syncthing-info/gui-credentials.txt` on VPS).
- [x] UFW `22000/tcp` open for peer sync.
- [x] nginx `/syncthing/` location added with `proxy_cookie_path` — page rendering works. Note: authenticated GETs via the nginx path return 403 due to Syncthing's strict CSRF + origin checks interacting poorly with subpath proxying. **Pairing/admin goes via SSH tunnel** (`ssh -L 8385:127.0.0.1:8384 root@VPS`). nginx path kept for read-only browsing from phone.
- [ ] Haim installs Syncthing on laptop, tunnels to VPS GUI, pairs device, accepts shared folder mapping to `~/Documents/Obsidian/TraderB/`, opens in Obsidian.

**Scheduled maintenance (system cron — NOT `openclaw cron`).**
Docs confirmed: `openclaw cron` is for agent-triggered jobs only (sends a message/system-event to an agent). For deterministic shell housekeeping, use system cron. Reserve `openclaw cron` for future agent-driven periodic work (e.g. "weekly Ari wiki review").

File: `/etc/cron.d/traderb-wiki` (installed, verified):
- Nightly 03:00 → `openclaw wiki compile`
- Weekly Mon 04:00 → `openclaw wiki lint`
- Nightly 03:30 → vault git auto-snapshot
- Logs to `/var/log/traderb-wiki.log`

DOD: `openclaw wiki status` green. Vault scaffolded. Obsidian on laptop opens synced folder and sees `index.md`, `inbox.md`, etc.

### Stage 0 verified state (as of 2026-04-14)

Working:
- memory-wiki plugin enabled; `openclaw wiki` CLI live (init, status, doctor, compile, lint, search, get all tested)
- Vault at `/opt/traderb-wiki` with canonical layout; git history intact (4 commits including the deliberate rollback)
- Syncthing service active, folder configured, GUI user/pass set, UFW open, nginx `/syncthing/` page renders
- System cron installed and verified
- `skills/dash-links.md` + updated `_shared/URLS.md` committed to `/opt/traderb`

Known residual warnings (from `openclaw doctor`, pre-existing — not Stage 0 gaps):
- `anthropic:claude-cli` auth profile missing in `/root/.openclaw/agents/main/agent/auth-profiles.json` → fix with `openclaw models auth login --provider anthropic --method cli --set-default` whenever convenient.
- Multiple state dirs detected (`/root/.openclaw` active + `/home/openclaw/.openclaw`). Pre-existing from earlier sessions. Revisit when we consolidate to one canonical state dir.
- 1/1 recent session missing transcript. Cleanup: `openclaw sessions cleanup --store /root/.openclaw/agents/main/sessions/sessions.json --enforce --fix-missing`.

Residual warnings (from `openclaw wiki doctor`, expected):
- Bridge has 0 exported memory artifacts — memory-core isn't exporting yet; resolved in Stage 1 when we start ingesting. Bridge config is correct, there's just nothing to bridge.

Not done (user-side, last mile):
- Laptop install of Syncthing + pair + open vault in Obsidian (see `Stage0_Laptop_Setup.md`).

Not a gap (nice-to-have deferred):
- Full interactive Syncthing GUI via `/syncthing/` (SSH tunnel is the reliable path).
- `anthropic:claude-cli` auth profile fix.

---

## Stage 1 — Seed and curate (3–5 days, no bot work)

Use memory-wiki's own ingest path — NOT hand-writing pages.

Ingest order (coarse → fine):
1. **Foundational docs.** `openclaw wiki ingest /opt/traderb/openclaw/PROJECT_VISION.md` (and `GOVERNANCE.md`, `SKILLS_REGISTRY.md`, `SELF_IMPROVEMENT.md`, `SPRINT_PLAN.md`).
2. **Persona SOUL/IDENTITY/AGENTS files** across all workspaces.
3. **Technical docs.** `/opt/traderb/docs/*` (DATA_MODEL, COMPARISON_MODEL, ARCHITECTURE, CONVENTIONS, GLOSSARY, TRADOVATE_RESEARCH).
4. **Decisions + memory.** `/opt/traderb/openclaw/decisions/` + `/opt/traderb/memory/` (or via `openclaw wiki bridge import` — let the plugin pull durable memory).
5. **Handoffs.** `/opt/traderb/openclaw/handoffs/`.
6. **Agent history.** Claude + Codex session histories from `~/.claude` and `~/.codex` — either via memory-wiki's ingest pipeline or a dedicated script.

After each batch:
- `openclaw wiki compile` — rebuild indexes and digests.
- `openclaw wiki lint` — flag contradictions, orphans, staleness.
- `openclaw wiki search <query>` — smoke-test retrieval.

Curation pass (Haim + Ari, joint sessions):
- [ ] Review the lint report (`_insights.md` equivalent). Fix contradictions.
- [ ] Promote approved pages — add `approved-by-haim: true` to frontmatter.
- [ ] Lower-confidence pages stay but agents can't cite them for trading decisions.
- [ ] Add 3–5 missing pages by ingesting fresh sources: "Why NQ over ES", "Pro-trader MCP scope", "Known failure modes".

DOD: Any TraderB concept resolves via `openclaw wiki search` to one page with provenance. No duplicates. No unresolved contradictions in lint.

---

## Stage 2 — Rewire personas around the wiki

- [ ] Every persona `SOUL.md` opens with: "Before any task: call `wiki_search`/`wiki_get` for relevant pages. Cite them in your plan. If no page exists for your domain, request one from project-lead before acting."
- [ ] `main` router includes relevant wiki page IDs in the task brief when spawning sub-agents.
- [ ] Vikunja rule (Ari owns): no task → `in_progress` without a linked wiki page for its DOD.
- [ ] **Librarian role** (extend Ari for now): runs `openclaw wiki compile` after any handoff lands, `wiki lint` weekly, resolves contradictions. Only Ari/Librarian applies wiki mutations unprompted; others request via handoff.
- [ ] Hook agents into compiled digest via `context.includeCompiledDigestPrompt: true` (already set in Stage 0). This gives personas structured context for near-zero extra cost.

DOD: Spawning Lior with a trivial task produces a plan citing wiki page IDs. If not, SOUL update failed.

---

## Stage 3 — NQ migration (first real bot work)

- [ ] Noa writes `concepts/nq-adaptation-spec.md` → `approved-by-haim: true`.
- [ ] Dan writes `concepts/nq-data-sources.md`. No baked data — every source named and live.
- [ ] Lior implements against those pages only. PR body cites page IDs.
- [ ] Yael tests against the pages. Regression snapshots on fixture days.
- [ ] Omer + Shira sign off against their own wiki pages (risk + compliance).
- [ ] Gate: `make regression` clean + 5 consecutive fixture-day passes.

DOD: Bot runs on Tradovate paper with NQ, trades match backtest on replay, every decision traceable to a wiki page.

---

## Stage 4 — Analytics + observability, designed for the pro-trader MCP

- [ ] Gal + Amit co-author `concepts/analytics-schema.md`.
- [ ] Every log line + metric carries `decision_ref` → wiki page ID.
- [ ] Observatory dashboard links metrics into the wiki. Optionally serve the vault read-only at `traderb.duckdns.org/wiki/` behind nginx (Quartz or similar renderer) for phone access.
- [ ] Rotem (UX) signs off on trader-readable display.

DOD: Haim clicks a trade in the observatory and lands on the wiki page that explains the rule that produced it.

---

## Stage 5 — Compliance + strategy iteration, parallel

- [ ] Shira codifies Topstep $50k Combine rules as enforced guards, each with `decision_ref` to its wiki page.
- [ ] Noa runs paper bot ~5–10 days, proposes strategy tweaks as new pages with status `proposed`.
- [ ] Approved tweaks → Vikunja tickets → Lior implements.

DOD: Simulated Topstep Combine passes on historical data + one clean strategy iteration loop.

---

## Stage 6 — Pro-trader MCP + live tweaks

MCP surface: `get_live_state()`, `get_recent_trades(window)`, `wiki_search(query)`, `wiki_get(id)`, `propose_tweak(spec)`.

- [ ] `propose_tweak` creates a Vikunja ticket + a `proposed` wiki page. Never executes.
- [ ] Haim reviews on Telegram, approves or rejects.
- [ ] Approved → Lior → Yael regressions → merge.

DOD: One full loop — live bot trades → MCP spots a pattern → proposes tweak → Haim approves → deployed → measurable effect.

---

## Stage 7 — Scale (deferred)

Multi-account prop firm scaling per `PROJECT_VISION` Phase 3. Revisit after Stage 6 stable 4+ weeks.

---

## Open Questions (resolve during Stage 0)

1. `vaultMode: isolated` vs `bridge` vs `unsafe-local` — start isolated for safety; revisit if we need cross-workspace sharing.
2. `vault.path = /opt/traderb-wiki` vs default `~/.openclaw/wiki/main` — keep at `/opt/traderb-wiki/` (peer to `/opt/traderb/`, outside OpenClaw internals, cleaner for backup + Syncthing).
3. Syncthing GUI via nginx `/syncthing/` or SSH tunnel only? Nginx is more convenient; SSH tunnel is more paranoid.
4. Official Obsidian CLI install method — verify the correct npm package name before running.
5. When to add read-only web view at `/wiki/` — Stage 4, once content exists.
6. Separate Anthropic API key for VPS agents vs shared Max quota — when?

---

## Risk Log

| Risk | Mitigation |
|------|------------|
| 1–2 weeks delay before bot work resumes | Accepted — fixes consistency cost |
| Agents stop using wiki after week 2 | Librarian audits via `wiki lint`; Ari blocks task progression if no wiki cite |
| Wiki drifts from code | Nightly `wiki compile`, PRs cite page IDs |
| Claude Max quota shared | Parallel track: API key for VPS |
| Vault git conflicts from simultaneous edits | Syncthing last-write-wins; git snapshots recover |
| memory-wiki plugin activation fails | Fall back: run `openclaw doctor`; worst case file a ticket upstream; vault files are just markdown so we can read them without the plugin |

---

## Immediate Next Step

Stage 0, starting with rollback of Ar9av install + reset of `/opt/traderb-wiki/`. Then memory-wiki config, init, doctor. Then Syncthing port + nginx + laptop pairing instructions.
