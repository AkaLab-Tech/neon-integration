# History

Completed work log. Newest first. Each entry references the PR(s) that delivered the work.

## 2026-07

### Guard destructive ops with in-CLI confirmation — 2026-07-01
**PR:** [#5](https://github.com/AkaLab-Tech/neon-integration/pull/5) — branch `fix/guard-destructive-ops`

Audit hardening: `branch-delete`, `project-create` and `project-delete` were
correctly excluded from the agent allowlist but had no in-CLI confirmation,
and `connstr` emitted live credentials with only prose warnings.

**Delivered:**
- In-CLI confirmation for `branch-delete` and `project-create` (`_confirm_gated`): explicit `--yes` flag or interactive `y/N`; `project-create` is gated because a new project incurs cost.
- Stronger gate for `project-delete` (`_confirm_typed`): interactively the operator must type the project id back verbatim; `--yes` bypasses for scripted operator use.
- Non-TTY stdin without `--yes` dies before `neonctl` is ever invoked, for all three commands.
- `connstr` now prints "warning: output contains live credentials — never write it to a tracked file or logs" to stderr; stdout remains the bare connection string for piping.
- Docs aligned: `usage()`, `skills/neon/SKILL.md`, `README.md`, `CLAUDE.md`.

**Tests:** `bash -n` + shellcheck clean; `--help` runs; with a fake `NEON_API_KEY` all three gated commands die at the guard pre-network without `--yes` on non-TTY stdin; stream test confirms the `connstr` warning goes to stderr while stdout carries only the neonctl output.
