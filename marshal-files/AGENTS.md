# MARSHAL — entry-point snippet (merge into your repo's AGENTS.md)

This file is **not** a sync source on its own. It is a short snippet that
should be **manually copied / merged into the host repository's root
`AGENTS.md`** so AI assistants pick up MARSHAL alongside any other
repo-level guidelines you keep there.

Keep this file **short**. The rich entry point lives in
[ENTRYPOINT.md](./ENTRYPOINT.md).

---

## Snippet to merge

This repository uses **MARSHAL** — an AI-assisted SDLC defined in
[marshal.md](../marshal.md).

Before doing any repo work:

1. Read [`.marshal/ENTRYPOINT.md`](./ENTRYPOINT.md) — it explains the
   process, the knowledge layer, and the available `marshal-*` skills and
   agents.
2. Read `.marshal/knowledge/INDEX.md` for the agent-maintained repo
   knowledge. Descend into folder / topic / subtopic indexes only as
   needed.
3. Honor the autonomy mode in `.marshal/config.yml` — by default,
   knowledge updates require human approval.

If the task is trivial (e.g. small docs typo) and does not require repo
knowledge, you may skip steps 2–3.

### AI-assistant config sync

MARSHAL works with
[ai-dev-agent-config-sync](https://github.com/crestreach/ai-dev-agent-config-sync) —
a small batch script that takes a single generic source tree of AI-assistant
configuration (`AGENTS.md`, `agents/`, `skills/`, `rules/`, `mcp-servers/`)
and fans it out into tool-native layouts: Cursor (`.cursor/`),
Claude Code (`.claude/` + `CLAUDE.md`), GitHub Copilot (`.github/`),
JetBrains Junie (`.junie/`), VS Code (`.vscode/`), plus a root `AGENTS.md`
and `.mcp.json`. Each tool consumes its own per-tool directory; the source
tree is the only place humans and agents edit.

Two layouts are supported:

- **Direct.** The sync's source root is `.marshal/` itself — simplest if
  MARSHAL's durable assets are the only thing the repo wants synced.
- **Separate `agent-config/` source tree.** The repo keeps its own
  `agent-config/` (or similarly named) folder at the root and uses the
  [`marshal-promote-assets`](./skills/marshal-promote-assets/SKILL.md)
  skill to copy `.marshal/{skills,agents,rules}/` into
  `agent-config/{skills,agents,rules}/` (with an `mx_` prefix on every
  promoted basename). Sync then runs over `agent-config/`.

When work touches **guidelines (the merged `AGENTS.md`), rules, skills,
subagents, or MCP server entries**, read the sync tool's local README
(typically `./ai-dev-agent-config-sync/README.md` if vendored as a
submodule, otherwise the upstream link above) for the source-tree format,
frontmatter fields, secret-token translation, and agent ↔ MCP linkage.
Author changes in the source tree (`.marshal/` for MARSHAL built-ins, or
`agent-config/` for repo-specific items), then re-run the sync — never
hand-edit the generated `.cursor/`, `.claude/`, `.github/`, `.junie/`,
`.vscode/` files, root `AGENTS.md`, `CLAUDE.md`, or `.mcp.json`.

### Hierarchical `AGENTS.md`

This repository follows the hierarchical `AGENTS.md` convention (the same
one Codex/Cursor and other AI tools recognize): the root `AGENTS.md` holds
repo-wide guidance, and any subdirectory **may** also contain its own
`AGENTS.md` with guidance scoped to that folder.

Rules:

- Per-folder `AGENTS.md` is **optional**. Only add one when a directory
  has rules, conventions, or context that genuinely differs from the
  rest of the repo.
- Scope is the folder it lives in plus all of its subfolders, unless a
  deeper `AGENTS.md` overrides a specific point.
- Closer `AGENTS.md` files **override** farther ones for overlapping
  guidance; non-overlapping guidance is additive.
- Before working in a folder, agents should read every `AGENTS.md` on
  the path from the repo root down to that folder, in order, and apply
  them with deeper files winning on conflicts.
- Keep each `AGENTS.md` short and focused; link out to longer docs
  rather than duplicating them.
