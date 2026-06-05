---
description: Set up neon-integration — link the CLI onto PATH, grant atelier agents the Neon allowlist (user-level), and wire this project's .env with NEON_API_KEY
allowed-tools: Bash(${CLAUDE_PLUGIN_ROOT}/scripts/atelier-neon:*), Read(.env), Edit(.env), Write(.env)
---

Set up the neon-integration plugin, then report a short summary. Do every step
in order:

1. Run the machine-wide part (PATH symlink + user-level allowlist merge — never
   atelier's per-task template):

   ```sh
   "${CLAUDE_PLUGIN_ROOT}/scripts/atelier-neon" configure --non-interactive
   ```

2. **Per-project auth.** Neon auth is per project. If the current directory is a
   project, wire its `.env`:
   - Read `.env` (create it if missing). If `NEON_API_KEY` is absent, ask the
     operator for a Neon API key (https://console.neon.tech/app/settings/api-keys)
     and add `NEON_API_KEY=<key>`. Never echo the key back.
   - Optionally add `NEON_PROJECT_ID` to scope commands to one project.
   - `.env` stays gitignored by atelier's `.env*` guardrail — never `git add` it.

3. Verify: `atelier-neon me`.

## Report

PATH link status, permissions merged, whether `.env` has `NEON_API_KEY` (key
presence only, never the value), whether `neonctl` is on PATH (or runs via
`pnpm dlx`), and the `me` result. Mention that `branch-delete` / `project-create`
/ `project-delete` always ask for confirmation.
