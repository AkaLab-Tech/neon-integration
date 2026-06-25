# CLAUDE.md

neon-integration is an optional, standalone Claude Code plugin that lets atelier
agents manage Neon serverless Postgres via the official Neon CLI (`neonctl`). It
supports listing and creating branches, fetching connection strings, and
inspecting projects, databases, and roles. It is independent of atelier and only
needed when the target project uses Neon.

## Stack

- **Language**: Bash (shell script — no compiled code)
- **Framework**: Claude Code plugin (`/.claude-plugin/plugin.json`), wraps `neonctl` CLI
- **Package manager**: pnpm (used as fallback runner: `pnpm dlx neonctl@latest`)
- **Test runner**: TBD — no test suite present
- **Linter / formatter**: TBD — no linter config found

## Architecture

The repo is a flat plugin layout with no `src/` or `lib/` tree:

- `scripts/atelier-neon` — the single executable: loads `.env`, wraps `neonctl`,
  handles permissions merging into user-level `settings.json`, and PATH setup.
- `skills/neon/SKILL.md` — the skill manifest consumed by atelier agents; documents
  available commands and the branch-per-task workflow.
- `commands/setup.md` — the `/neon-integration:setup` slash command spec.
- `.claude-plugin/plugin.json` — Claude Code plugin manifest (name, version, author).

## Conventions

- **CLI entry point**: `scripts/atelier-neon <command>` — run `atelier-neon --help`
  for the full reference after linking via `atelier-neon link-cli`.
- **Setup**: `atelier-neon configure` (machine-wide) then add `NEON_API_KEY` to the
  target project's `.env`.
- **Permissions**: `atelier-neon enable-permissions` / `disable-permissions` merge
  the allowlist into user-level `settings.json` (idempotent, order-preserving).
- **Gated commands**: `branch-delete`, `project-create`, `project-delete` are
  intentionally absent from the allowlist — always require operator confirmation.
- **CI**: no GitHub Actions workflows present.

## What this project is NOT

- TBD

## Out of scope for AI agents

- Never write `connstr` output into a tracked file — connection strings are
  live credentials and belong only in a gitignored `.env`.
- Never `git add .env` or any file matching `.env*`.
- `branch-delete`, `project-create`, and `project-delete` must not be run without
  explicit operator confirmation (they are not in the permissions allowlist).
