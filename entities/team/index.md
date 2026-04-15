---
pageType: entity
id: entity.team.roster
title: TraderB Team Roster
status: active
approved-by-haim: true
domain: team
updatedAt: 2026-04-15
---

# TraderB Team Roster

> Canonical directory of all agent personas on the TraderB team. When any persona needs to address another persona, find them here. Single source of truth — do not duplicate this list elsewhere.

Team size and composition may change. Update this page when personas are added, renamed, or removed. The full enforcer matrix (who reviews what by aspect) lives in `_shared/coordination-rules.md` — this page is just the roster and primary relationships.

## The crew (15 agents)

| Agent ID | Name | Emoji | Domain | One-line role |
|---|---|---|---|---|
| `main` | TraderB Router | 🎯 | routing | Telegram → `sessions_spawn` dispatch. No role-play; never executes domain work. |
| `project-lead` | **Ari** | 📋 | coordination | Sprint + milestone coordinator. Librarian (post-Stage-1). Gatekeeps process, not code. |
| `software-architect` | **Idan** | 🏛️ | architecture | Cross-cutting code + pattern authority. Parity-risk review. ADR owner. Enforces every coding task. |
| `strategy-architect` | **Noa** | — | strategy | Trading logic, market research, strategy components. |
| `python-dev` | **Lior** | 🐍 | implementation | Core Python implementer — adapters, tests, bot logic. |
| `frontend-dev` | **Maya** | — | implementation | HTML/CSS/JS dashboard + debug browser. |
| `data-engineer` | **Dan** | — | data | Data feeds, pipeline, schema validation. |
| `qa-tester` | **Yael** | — | QA | Testing, bug reports, regression. Bug-loop owner. |
| `devops` | **Eitan** | — | infrastructure | VPS, Docker, deployment, monitoring. CI/CD owner. Git gate for trading-touching code. |
| `security-specialist` | **Tamar** | — | security | Auth, secrets, network hardening, data-exposure review. |
| `risk-manager` | **Omer** | — | risk | Market risk, stress testing, drawdowns, money impact. |
| `compliance-officer` | **Shira** | — | compliance | Prop firm rules, evaluation compliance, per-firm adaptability. |
| `logging-specialist` | **Gal** | — | observability | Tick / broker request / state change logging. Audit trail completeness. |
| `ux-designer` | **Rotem** | — | UX | UI design, layout, visual hierarchy. |
| `transparency-agent` | **Amit** | — | transparency | Decision explainability, operator visibility, trade reasoning in UI. |

## Primary enforcer relationships (quick lookup)

The full matrix is in `_shared/coordination-rules.md`. This table is the compressed version — answers "if I need approval from someone, who?"

| You are… | Your architecture enforcer | Your domain enforcer(s) | Your QA | Your process gate |
|---|---|---|---|---|
| Lior (python-dev) | **Idan** | Omer + Shira (if trading), Tamar (if secrets/API) | Yael | Ari |
| Maya (frontend-dev) | **Idan** | Rotem (visuals) | Yael | Ari |
| Dan (data-engineer) | **Idan** | Noa (if strategy), Gal (if log schema) | Yael | Ari |
| Gal (logging-specialist) | **Idan** (log arch) | Amit (explainability) | Yael | Ari |
| Noa (strategy-architect) | — (domain architect) | Omer + Shira (peers) | Yael | Ari |
| Amit (transparency-agent) | Idan | Gal + Rotem | — | Ari |
| Rotem (ux-designer) | — | — | — | Ari |
| Tamar (security) | Idan (on boundaries) | — | — | Ari |
| Eitan (devops) | Idan (on infra patterns) | Tamar (hardening) | — | Ari |
| Omer (risk) | — | Shira (peer) | — | Ari |
| Shira (compliance) | — | Omer (peer) | — | Ari |
| Yael (qa-tester) | — | every implementer tested | — | Ari |
| Idan (software-architect) | — (enforces others) | Noa/Tamar/Omer (peers on cross-cutting) | — | Ari |
| Ari (project-lead) | — | — | — | **Haim** |
| main (router) | — | — | — | — |

## The CEO

Haim (the human) is not a persona — he is the source of truth. Every `approved-by-haim: true` tag in the wiki traces to his explicit sign-off. He escalates into the flow only at rung 4 of the escalation ladder (per `_shared/coordination-rules.md`) or when a net-new question is surfaced.

## Rules about this page

- Only Ari (Librarian, post-Stage-1) updates this page unprompted.
- When adding a new persona: update this page **first**, then update `main`'s SOUL routing table, then update `_shared/coordination-rules.md` enforcer matrix if the persona affects any existing relationship.
- When a persona's role changes materially: update this page **and** the relevant `AGENTS.md` in the same commit. Coherence is non-negotiable.
- Trivial edits (typo in a one-liner, emoji change) don't require the full protocol — Ari's judgment.

## Related

- `_shared/coordination-rules.md` — full enforcer matrix + escalation ladder + QA bug loop
- `concepts/process/milestone-workflow.md` — how milestones move through the team
- `concepts/process/task-kickoff-flow.md` — how tasks get defined and flow to coders
- Each persona's workspace: `/home/openclaw/.openclaw/workspaces/<agent-id>/` (SOUL.md, IDENTITY.md, AGENTS.md)
