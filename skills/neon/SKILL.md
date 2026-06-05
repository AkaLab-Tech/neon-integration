---
name: neon
description: >-
  Manage Neon serverless Postgres via the atelier-neon CLI (a thin wrapper over
  the official Neon CLI). Use when the operator asks to list or create database
  branches, get a connection string, inspect projects/databases/roles, or
  provision/delete Neon resources.
---

# Neon

`atelier-neon` wraps the official Neon CLI (`neonctl`). It reads `NEON_API_KEY`
(and optional `NEON_PROJECT_ID`) from the current project's `.env`. Run
`atelier-neon --help` for the full reference.

## Commands

| Command | Purpose |
| --- | --- |
| `atelier-neon me` | Current Neon user |
| `atelier-neon projects` | List projects |
| `atelier-neon branches` | List branches |
| `atelier-neon databases` | List databases |
| `atelier-neon roles` | List roles |
| `atelier-neon operations` | Recent operations |
| `atelier-neon connstr [branch]` | Get a connection string |
| `atelier-neon branch-create [name]` | Create a branch |
| `atelier-neon branch-delete <id\|name>` | Delete a branch (operator confirmation) |
| `atelier-neon project-create [name]` | Create a project (operator confirmation) |
| `atelier-neon project-delete <id>` | Delete a project (operator confirmation) |

## Branch-per-task flow

1. `branch-create <task-slug>` — an isolated DB branch for the task's work.
2. `connstr <task-slug>` — the connection string to point the app/migrations at.
3. Run migrations / tests against that branch.
4. When merged, the operator can `branch-delete <task-slug>` (gated).

## Rules

- Resolve targets with `projects` / `branches` first; never guess an id.
- `branch-delete`, `project-create`, and `project-delete` are not in the
  allowlist — confirm with the operator before running them.
- `connstr` returns live database credentials — never write its output into a
  tracked file; the project `.env` (gitignored) is the place for it.
- If `NEON_PROJECT_ID` is set in `.env`, scoped commands target that project
  automatically.
