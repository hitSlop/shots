# Shots Distribution Runbook

This repo is packaged for Codex plugin discovery and hosted MCP server discovery.

## Targets

| Target | Purpose | Status |
| --- | --- | --- |
| Codex repo marketplace | CLI and app install from `hitSlop/shots` | Ready |
| Codex workspace sharing | Private workspace installs from Codex app | Manual share step |
| Awesome Codex Plugins / HOL Registry | Public Codex plugin directory and trust score | PR open: <https://github.com/hashgraph-online/awesome-codex-plugins/pull/277> |
| Official MCP Registry | Public MCP server discovery as `run.shots/shots` | Published |
| Glama | Third-party MCP discovery | Listed: <https://glama.ai/mcp/servers/hitSlop/shots> |
| PulseMCP | Third-party MCP discovery | Pending index from official MCP Registry |
| MCP.so | Third-party MCP discovery | Manual sign-in submission |
| `punkpeye/awesome-mcp-servers` | Community MCP list | PR open: <https://github.com/punkpeye/awesome-mcp-servers/pull/9592> |
| GitHub topics | Organic discovery | Added |

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

After the scanner passes on GitHub Actions, prepare a PR:

```bash
scripts/prepare-awesome-codex-pr.sh
```

Before marking the PR ready for review, paste the passing HOL scanner run URL or score into the PR body.

Current PR:

```text
https://github.com/hashgraph-online/awesome-codex-plugins/pull/277
```

## MCP Registry

Shots publishes as:

```text
run.shots/shots
```

The hosted endpoint is:

```text
https://shots.run/api/mcp
```

The hosted endpoint is published in the official registry. If it ever needs to be republished through GitHub Actions:

1. Generate an Ed25519 keypair for MCP Registry domain authentication.
2. Host the public proof at `https://shots.run/.well-known/mcp-registry-auth`.
3. Add the private key hex as the GitHub repository secret `MCP_PUBLISHER_PRIVATE_KEY`.
4. Run the `MCP Registry` workflow with `publish=true`.

The workflow validates `server.json` on every relevant PR and only publishes on manual dispatch.

Official registry check:

```bash
curl 'https://registry.modelcontextprotocol.io/v0.1/servers?search=run.shots%2Fshots'
```

## MCP.so

Submit manually at <https://mcp.so/submit> after signing in.

Use:

```text
Type: MCP Server
Name: Shots
URL: https://github.com/hitSlop/shots
```

Server Config:

```json
{
  "mcpServers": {
    "shots": {
      "type": "http",
      "url": "https://shots.run/api/mcp"
    }
  }
}
```

Unauthenticated API submissions return `{"code":-1,"message":"no auth, please login"}`.

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
