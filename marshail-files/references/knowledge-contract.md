# Knowledge Contract

This is the tool-agnostic MARSHAIL contract for agent-managed knowledge.
Knowledge is MARSHAIL's mid- and long-term memory: durable repo facts, rationale, decisions, conventions, and reusable learnings that survive across changes.
It is also a body of **notes about the code** and a **map that helps narrow down code searches** — pointers from concepts and areas to where the relevant code lives, so agents can find the right files without re-exploring the whole repo each time.

The contract defines what any knowledge representation must provide.
The active implementation is configured separately by `knowledge.representation_ref` in `.marshail/config.yml`, which points at the implementation's reading guide (see *Implementation reference: reading guide and maintenance guide*).

## Goals

- Give agents a **reliable map of where to look next** without loading half the repo into context.
- Capture **enough analyzed depth** that an agent can understand an area's behavior, contracts, and risky parts *without* re-scanning the code — the knowledge is the cached result of a code scan that already happened, not a table of contents.
- Derive knowledge **primarily from the code itself** (read from entrypoints inward), not only from pre-existing prose docs; pre-existing docs are corroborating input, never the sole source.
- Separate **stable repo knowledge** (logic, architecture, conventions, invariants) from **process learnings** (the per-phase outputs of MARSHAIL).
- Make knowledge **diffable and reviewable** (text in git).
- Allow knowledge to **evolve with the code** without git hooks or background daemons, via explicit dated stamps and an explicit maintenance step.
- Keep the system **tool-agnostic** by keeping the knowledge body behind this exchangeable representation reference.

## Two trees

MARSHAIL separates the config-sync sources from the knowledge tree:

| Tree | Owner | Content | Fanned out to tool layouts? |
|---|---|---|---|
| `<user-config-source>/` (e.g. `.agent-config/`) | user / project | project-specific `agents/`, `skills/`, `rules/`, `mcp-servers/`, `AGENTS.md` | yes, via [cyncia](https://github.com/crestreach/cyncia) |
| `.marshail/` (config-sync source) | MARSHAIL baseline | `marshail-*`-prefixed `agents/`, `skills/`, `rules/`, plus `AGENTS.md` | yes, via the same sync tool |
| `.marshail/knowledge/` | agents (under human approval) | repo knowledge: code notes, logic, architecture, decisions, learnings | no |

The first two are **config-sync sources**: the cyncia sync fans them out into tool-native layouts (`.cursor/`, `.claude/`, `.github/`, …).
The knowledge tree is excluded from that sync — it stays under `.marshail/knowledge/` and is read in place.
Agents reach it as `.marshail/ENTRYPOINT.md` instructs: an always-loaded root slice first, then progressively deeper folder indexes and topic files as a task needs them.

## Implementation reference: reading guide and maintenance guide

`knowledge.representation_ref` points at the implementation's **reading guide** — the document every agent that reads knowledge loads (it is part of the **standard** load tier in [activation-protocol.md](activation-protocol.md)).
It must be enough on its own to find, read, and understand knowledge, and to write an inbox note (see *Fixed locations and the inbox*):

- it **names the root entry** (the file an agent opens first) and the read order from there;
- it defines the metadata fields and what they mean, including which field carries the **scope** and which the **freshness marker** (see *Required metadata semantics*);
- it defines how cross-references are expressed and followed;
- it states what an agent does when it notices a topic is stale while reading.

Because every standard-tier agent loads it on activation, the reading guide should stay compact — roughly 100 lines is a good target (a suggestion, not a hard limit).

Everything needed only to **maintain** knowledge — organizing / splitting rules, depth and derivation policy, index regeneration, size limits, staleness detection, the update protocol — should live in a separate **maintenance guide**, linked from the reading guide and loaded only as needed (the **full** load tier: the knowledge curator and any agent that writes canonical knowledge).
A separate maintenance guide is recommended, not mandatory; an implementation may keep both in one file, at the cost of a heavier standard tier.
Implementation-specific settings (e.g. size caps) belong in their own top-level section of `.marshail/config.yml`, named after the implementation, so the shared `knowledge:` section stays implementation-neutral.

Other MARSHAIL files (agents, skills, the entry point) refer to knowledge in the **contract's terms** — "the root entry", "a unit's scope", "its freshness marker", "the inbox" — and leave the concrete file names and field names to the active implementation.

## Fixed locations and the inbox

Two locations are fixed by this contract, independent of the implementation, because the MARSHAIL process itself depends on them:

- **`.marshail/knowledge/learn/inbox/`** — the staging area for knowledge-shaped notes that are not yet canonical.
  Any agent may drop a note here (subject to `knowledge.capture_during_process`); only the curator promotes from it.
  The reading guide defines the note's shape; by default a note carries the same metadata as a topic, minus whatever the curator fills in on promotion.
- **Decision records** (lightweight ADRs) must be discoverable from the root entry; the implementation defines their location (the default implementation uses `.marshail/knowledge/decisions/`).

## Required capabilities

A MARSHAIL knowledge representation must define:

- where canonical knowledge is stored;
- how agents discover available knowledge from a cheap root entry point, and what that root entry is called;
- how topic files or records expose a stable identity, summary, scope, and freshness metadata;
- how agents select the smallest useful knowledge slice for a task;
- how knowledge items **cross-reference** each other — a topic or record may link to related knowledge content (other topics, decisions, indexes) so related knowledge is connected instead of duplicated;
- how knowledge is produced at an **appropriate, locally-decided analysis depth** — derived from a code scan that follows the system's entrypoints and call paths into its complex and high-value areas, and split into as many levels of hierarchy as each area needs (no fixed number of levels);
- how updates are proposed, reviewed, and applied under `.marshail/config.yml` autonomy settings;
- how stale knowledge is detected against changed repo paths;
- how knowledge is split, merged, archived, or rebuilt as the repo changes;
- how deltas from research and phase learnings are promoted into canonical knowledge.

## Required metadata semantics

The representation may use files, records, generated indexes, or a retrieval sidecar, but it must expose these semantics to agents:

- stable `id` or equivalent key;
- `summary` for cheap discovery;
- **scope** — what code or domain the unit covers, usually repo paths or domain ownership; this is what maps changed code to the knowledge it may invalidate, and what points an agent at the right files;
- importance or loading priority;
- confidence or verification quality;
- **freshness marker** — when, and against which source revision (usually a commit), the unit's claims were last verified;
- links to related knowledge items (cross-references), plus parent/child relationships when knowledge is split.

The exact field names belong to the active implementation's reading guide.
Agents and skills outside the implementation refer to these semantics by the names above (scope, freshness marker), not by implementation field names.

## Required update protocol

All write paths honor `knowledge.autonomy` in `.marshail/config.yml`:

- `review`: produce a diff or equivalent reviewable change set and wait for human approval.
- `auto`: apply the update directly and report what changed.

Knowledge updates must be traceable to one of these sources:

- initial repo scan (`marshail-knowledge-init`);
- changed repo paths after an implementation cycle (`marshail-knowledge-maintain from-changes`);
- reusable phase learnings (`marshail-knowledge-maintain from-learning`);
- scheduled or requested freshness checks (`marshail-knowledge-maintain rescan`);
- branch reconciliation (`marshail-knowledge-branch-merge`);
- larger structural rebuild (`marshail-knowledge-rebuild`).
