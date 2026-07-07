# Shots Distribution Runbook

This repo is packaged for Codex plugin discovery and hosted MCP server discovery.

## Targets

| Target | Purpose | Status |
| --- | --- | --- |
| Codex repo marketplace | CLI and app install from `hitSlop/shots` | Ready |
| Codex workspace sharing | Private workspace installs from Codex app | Manual share step |
| Awesome Codex Plugins / HOL Registry | Public Codex plugin directory and trust score | Ready after CI passes |
| Official MCP Registry | Public MCP server discovery as `run.shots/shots` | Ready after domain auth |
| Glama / PulseMCP / Smithery | Third-party MCP discovery | Submit after MCP Registry publish |
| `punkpeye/awesome-mcp-servers` | Community MCP list | Submit PR after registry publish |
| GitHub topics | Organic discovery | Add in repository settings |

## Validation

Run these from the repo root:

```bash
python3 scripts/check-plugin-package.py .
uv run --with pyyaml /Users/jordan/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py "$PWD"
UV_CACHE_DIR=/private/tmp/uv-cache uvx --from plugin-scanner plugin-scanner scan . --format text --min-score 80 --fail-on-severity high
mcp-publisher validate server.json
```

Expected local scanner result: score `100/100`, no critical/high/medium/low/info findings.

## Codex

Install as a Git-backed repo marketplace:

```bash
codex plugin marketplace add hitSlop/shots
codex plugin add shots@shots
codex mcp login shots
codex mcp get shots
```

In the Codex app, workspace sharing is manual: Plugins -> Created by you -> Shots -> Share.

## Awesome Codex Plugins

Requirements are implemented in this repo:

- `.github/workflows/hol-plugin-scanner.yml`
- `SECURITY.md`
- `LICENSE`
- `.codex-plugin/plugin.json`
- `.codexignore`
- `README.md`

After the scanner passes on GitHub Actions, prepare a draft PR:

```bash
scripts/prepare-awesome-codex-pr.sh
```

Before marking the PR ready for review, paste the passing HOL scanner run URL or score into the PR body.

## MCP Registry

Shots publishes as:

```text
run.shots/shots
```

The hosted endpoint is:

```text
https://shots.run/api/mcp
```

To publish through GitHub Actions:

1. Generate an Ed25519 keypair for MCP Registry domain authentication.
2. Host the public proof at `https://shots.run/.well-known/mcp-registry-auth`.
3. Add the private key hex as the GitHub repository secret `MCP_PUBLISHER_PRIVATE_KEY`.
4. Run the `MCP Registry` workflow with `publish=true`.

The workflow validates `server.json` on every relevant PR and only publishes on manual dispatch.

## GitHub Topics

Recommended topics:

```text
mcp
mcp-server
codex-plugin
codex-skills
app-store-screenshots
app-icon-generator
aso
screenshot-generator
app-store-optimization
localization
```
