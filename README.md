# neon-integration

Optional, standalone [Claude Code](https://docs.anthropic.com/claude/docs/claude-code)
plugin that lets [atelier](https://github.com/AkaLab-Tech/atelier) agents manage
[Neon](https://neon.tech) serverless Postgres via the **official Neon CLI**
(`neonctl`): list/create branches, get connection strings, and inspect
projects, databases, and roles.

It is independent of atelier; install it only if you use Neon.

## Install

```
/plugin marketplace add AkaLab-Tech/claude-plugins
/plugin install neon-integration@akalab-tech
```

## Setup

Get a Neon API key at <https://console.neon.tech/app/settings/api-keys>. Setup
has a **machine-wide** part (link the CLI onto `PATH`, grant the
[allowlist](#how-permissions-work)) and a **per-project** part (the key), so one
operator can point different projects at different Neon projects.

**With atelier** — pick it during `install.sh`, or run any time:

```
/atelier:setup-neon
```

**Standalone** — `configure` does the machine-wide part, then (from inside a
project) wires that project's `.env`:

```sh
atelier-neon configure
# or, inside a Claude Code session:
/neon-integration:setup
```

`atelier-neon` uses an installed `neonctl` if present, otherwise
`pnpm dlx neonctl@latest` (zero global install).

### Per-project auth

Each project carries its own credentials in its `.env` (kept gitignored by
atelier's `.env*` guardrail):

```sh
NEON_API_KEY=<key>
# optional, to scope commands to one project:
NEON_PROJECT_ID=<project-id>
```

## How permissions work

This plugin never edits atelier's shipped `settings.template.json`.
`/neon-integration:setup` (or `atelier-neon enable-permissions`) merges the
read/write allowlist into your **user-level** `settings.json`
(`$ATELIER_CONFIG_DIR/settings.json`) — idempotent, order-preserving, no
clobber. The gated commands (`branch-delete`, `project-create`, `project-delete`)
are deliberately omitted so they fall back to operator confirmation. Undo with
`atelier-neon disable-permissions`.

## Usage

Run `atelier-neon --help`, or let the `neon` skill drive it.

## License

MIT — see [LICENSE](LICENSE).
