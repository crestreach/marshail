# AGENTS.md

Guidance for AI agents working in the MARSHAL repository.

This repo defines **MARSHAL** — a process for AI-assisted software delivery. The canonical spec lives in [`marshal.md`](./marshal.md); the project overview is in [`README.md`](./README.md).

## Scope

Most work here is editing the process spec and adding supporting tooling (skills, prompts, templates, examples). The process itself is still being shaped, so this file is intentionally minimal and will grow as conventions stabilize.

## Working rules

- Treat [`marshal.md`](./marshal.md) as the single source of truth for the method. Any method change lands there first, then propagates to `README.md` and this file.
- Keep the MARSHAL acronym intact.
- Don't duplicate process detail here or in `README.md` — link to `marshal.md` instead.
- Don't commit unless explicitly asked.

Deeper agent conventions (stage-by-stage behavior, approval gates, learning discipline, templates, etc.) will be added here once the process stabilizes.
