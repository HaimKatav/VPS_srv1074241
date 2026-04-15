---
pageType: source
id: source.vocabulary-and-naming
title: vocabulary and naming
sourceType: local-file
sourcePath: /opt/traderb-wiki/concepts/meta/vocabulary-and-naming.md
ingestedAt: 2026-04-15T22:43:29.225Z
updatedAt: 2026-04-15T22:43:29.225Z
status: active
---

# vocabulary and naming

## Source
- Type: `local-file`
- Path: `/opt/traderb-wiki/concepts/meta/vocabulary-and-naming.md`
- Bytes: 5641
- Updated: 2026-04-15T22:43:29.225Z

## Content
````text
---
pageType: concept
id: concept.meta.vocabulary-and-naming
title: Vocabulary and Naming
status: active
approved-by-haim: true
domain: meta
updatedAt: 2026-04-15
---

# Vocabulary and Naming

> Every canonical artifact in TraderB — a component, a feature, a process, a persona, a concept — gets one name that everyone uses. Shared vocabulary is how the team holds coherence across roles, sessions, and time.

## Why this matters

- **Agents that talk about the same thing by different names** can't coordinate. `python-dev`'s "chart widget" vs `frontend-dev`'s "graph panel" vs `ux-designer`'s "Interactive Graph" = three different bugs logged, three different specs, zero shared understanding.
- **Graph-view connections in Obsidian work** only when pages cite each other by consistent names/paths. Fragmented vocab = fragmented graph = useless navigation.
- **Wiki search** returns the right page only if the searched term matches canonical language. "FVG" must not be "fair value gap" in one doc and "imbalance zone" in another.
- **New personas (or human collaborators) onboard** fast when the vocabulary is stable. If names drift, every newcomer relearns what things are called.

## The five naming rules

### 1. One canonical name per thing

Every named artifact (component, feature, process, persona, concept, tool, protocol) has exactly ONE canonical name. All other references in docs, code, and chat use that name.

**Examples:**
- UI: "Interactive Graph" (not "chart widget," not "price graph," not "dashboard chart")
- Process: "task-kickoff-flow" (not "task pickup," not "task intake")
- Persona: "`qa-manager`" (not "QA lead," not "test coordinator")
- Tool: "Graph tool" (reusable component — see [[concepts/architecture/reusable-tools|reusable-tools]])

### 2. Canonical name + code identifier are linked

Human-readable canonical names often have corresponding code identifiers (class names, module paths). Every entity page (especially [[entities/components|component entities]]) documents both:

```
- Canonical name: Interactive Graph
- Code identifier(s): InteractiveGraphWidget (dashboard.html), render_graph() (observatory.py)
```

Renames happen together: rename in code = rename in canonical = rename in docs = rename in chat.

### 3. Connections are explicit via wikilinks

Every cross-reference in a canonical page uses Obsidian wikilinks (`[[canonical-path|display-name]]`). See the 2026-04-15 hygiene sweep commit for the pattern.

Path references remain for things that live outside the wiki vault (code files, external docs). Wikilinks are for in-vault connections.

### 4. Minimal loading — scope pages tight

A canonical page covers ONE concept, not a cluster. If a page covers three things, split it.

Examples of tight scope:
- `concepts/architecture/broker-portability.md` = just the protocol invariant; doesn't include strategy details
- `concepts/architecture/config-lineage.md` = just trade-to-config traceability; doesn't include toggle mechanics
- `concepts/architecture/configurable-strategy-components.md` = just toggle mechanics

When a concept grows, split rather than stuff. Pages that agents load must be short enough to load fast.

### 5. Easy lookup — name = find

A new team member should be able to find any canonical artifact in ≤3 tries:
- Try 1: Obsidian search by name
- Try 2: `openclaw wiki search <name>`
- Try 3: graph view traversal from a known adjacent page

If the lookup fails, the name is wrong or the page is misplaced. Fix the vocabulary.

## When a new term is introduced

Per [[concepts/process/task-kickoff-flow|task-kickoff-flow]], the enforcer introducing a new term in their domain breakdown includes in their handoff to `project-lead`:

- **Proposed canonical name** (human + code)
- **Current synonyms in use** (what we're replacing)
- **Why this name** (semantic rationale)
- **Migration plan** if this replaces an existing inconsistent term

`project-lead` holds the term during coordination (step 3). If it survives alignment (no enforcer conflicts), `project-lead` records it in an entity page — usually `entities/components/<name>.md` for components, `entities/<domain>/<name>.md` for other things.

## Enforcement

- **Every enforcer verifies** naming consistency in submissions they review — per [[concepts/meta/documentation-hygiene|documentation-hygiene]].
- **`project-lead` runs `openclaw wiki lint`** weekly; the lint catches orphans, contradictions, and (eventually) synonym drift if we instrument for it.
- **`software-architect` specifically** watches code-level naming in every review — code identifier must match the documented canonical.
- **Haim catches drift** at hands-on testing — if he hears a different name than what he expects, that's a signal.

## Interaction with other canonical pages

- [[concepts/meta/documentation-hygiene|documentation-hygiene]] — the enforcement chain for naming + other DNA rules
- [[concepts/meta/wiki-information-architecture|wiki-information-architecture]] — where canonical pages live in the folder structure
- [[entities/components/_template|components template]] — where component naming is operationalized per-component
- [[concepts/architecture/reusable-tools|reusable-tools]] — tools need canonical names from day one

## What this is NOT

- Not a style guide (that's separate — lives in `docs/CONVENTIONS.md` for code, not in the wiki).
- Not a prohibition on humor or personality in writing — names should be precise, but writing stays readable.
- Not a rule against renaming — we rename when the old name is wrong. The rule is: rename everywhere at once, atomically.

````

## Notes
<!-- openclaw:human:start -->
<!-- openclaw:human:end -->

## Related
<!-- openclaw:wiki:related:start -->
- No related pages yet.
<!-- openclaw:wiki:related:end -->
