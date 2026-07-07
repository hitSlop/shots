# Shots Distribution Runbook

This repo is packaged for Codex plugin discovery and hosted MCP server discovery.

## Targets

| Target | Purpose | Status |
| --- | --- | --- |
| Codex repo marketplace | CLI and app install from `hitSlop/shots` | Ready |
| Codex workspace sharing | Private workspace installs from Codex app | Manual share step |
| Awesome Codex Plugins / HOL Registry | Public Codex plugin directory and trust score | PR open: <https://github.com/hashgraph-online/awesome-codex-plugins/pull/277> |
| Official MCP Registry | Public MCP server discovery as `run.shots/shots` | Published |
| Smithery | Third-party MCP gateway & discovery | Unblocked (OAuth redirect fix deployed) |
| Glama | Third-party MCP discovery | Listed: <https://glama.ai/mcp/servers/hitSlop/shots> — was "Unhealthy", unblocked by OAuth redirect fix |
| PulseMCP | Third-party MCP discovery (auto-indexes from official registry) | Pending |
| MCP.so | Third-party MCP discovery | Manual sign-in submission at <https://mcp.so/submit> |
| mcpservers.org | Third-party MCP discovery (wong2) | Submitted at <https://mcpservers.org/submit> |
| `punkpeye/awesome-mcp-servers` | Community MCP list | PR open: <https://github.com/punkpeye/awesome-mcp-servers/pull/9592> |
| GitHub topics | Organic discovery | Added |

## OAuth Redirect URI Fix (for external MCP gateways)

Smithery and Glama use their own HTTPS domains as OAuth redirect URIs (e.g. `https://shots--jordan-u190.run.tools/callback`). Before the fix, only loopback redirect URIs were allowed.

After deploying the fix in `convex/mcpServer/oauthCodes.ts` and `apps/web/src/features/install/connect-page.tsx`:

1. Register the gateway as an OAuth client in the `oauthClients` table with its `redirectUris`
2. The backend validates HTTPS redirect URIs against the client's registered URIs
3. PKCE S256 is still mandatory

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

## MCP Directories & Registries

### Tier 1 — MCP-specific directories

| Directory | URL | Submission | Notes |
| --- | --- | --- | --- |
| Official MCP Registry | <https://registry.modelcontextprotocol.io/> | GitHub Actions workflow | Published as `run.shots/shots` |
| Smithery | <https://smithery.ai> | Submit via site | Requires OAuth client with registered `redirectUris` |
| Glama | <https://glama.ai/mcp> | Auto-detected from GitHub | Listed at <https://glama.ai/mcp/servers/hitSlop/shots> |
| PulseMCP | <https://pulsemcp.com> | Auto-indexes from official registry | Pending |
| MCP.so | <https://mcp.so> | <https://mcp.so/submit> (sign-in required) | Not yet submitted |
| mcpservers.org | <https://mcpservers.org> | <https://mcpservers.org/submit> | Submitted |
| LobeHub | <https://lobehub.com> | Submit via site | Not yet submitted |
| ModelContextProtocol.info | <https://modelcontextprotocol.info/tools/registry/> | Submit via site | Not yet submitted |

### Tier 2 — Awesome lists (MCP)

| Repository | Status |
| --- | --- |
| `punkpeye/awesome-mcp-servers` | PR open: <https://github.com/punkpeye/awesome-mcp-servers/pull/9592> |
| `appcypher/awesome-mcp-servers` | PR created: `Jonovono:awesome-mcp-servers-1:add-shots` |
| `abordage/awesome-mcp` | PR created: `Jonovono:awesome-mcp:add-shots` |
| `TensorBlock/awesome-mcp-servers` | PR created: `Jonovono:awesome-mcp-servers-2:add-shots` |
| `mctrinh/awesome-mcp-servers` | Not yet submitted |
| `wong2/awesome-mcp-servers` | Does NOT accept PRs — use mcpservers.org instead |

### Tier 3 — Awesome lists (non-MCP)

| Repository | Category | Status |
| --- | --- | --- |
| `hashgraph-online/awesome-codex-plugins` | Codex plugins | PR open: <https://github.com/hashgraph-online/awesome-codex-plugins/pull/277> |
| `rafaecheve/Awesome-ASO` | ASO tools | PR created: `Jonovono:Awesome-ASO:add-shots` |
| `sanketfirodiya/ios-marketing-resources` | iOS marketing | PR created: `Jonovono:ios-marketing-resources:add-shots` |
| `Piebald-AI/awesome-gemini-cli-extensions` | Gemini CLI | PR created: `Jonovono:awesome-gemini-cli-extensions:add-shots` |
| `hao-ji-xing/awesome-cursor` | Cursor MCPs | PR created: `Jonovono:awesome-cursor:add-shots` |

### Tier 4 — AI agent skill marketplaces

| Directory | URL | Notes |
| --- | --- | --- |
| Agensi | <https://www.agensi.io> | Security-scanned skills, 70/30 creator payments |
| ClaudeSkills.info | <https://claudeskills.info> | 658+ free community skills |
| Claude Code Marketplaces | <https://claudemarketplaces.com> | Aggregator of 2,500+ marketplaces |
| AgentSkill.sh | <https://agentskill.sh> | 44K+ skills with security scanning |
| Cross AI Tools | <https://crossaitools.com> | Multi-platform agent marketplace |
| Rube by Composio | <https://rube.composio.dev> | Composio agent platform |
| cursor.directory | <https://github.com/pontusab/cursor.directory> | Cursor rules/extensions directory |
| `ComposioHQ/awesome-claude-plugins` | <https://github.com/ComposioHQ/awesome-claude-plugins> | GitHub awesome list |
| `ComposioHQ/awesome-claude-skills` | <https://github.com/ComposioHQ/awesome-claude-skills> | GitHub awesome list |

### Tier 5 — Product launch & general directories

| Directory | URL |
| --- | --- |
| Product Hunt | <https://www.producthunt.com> |
| DevHunt | <https://devhunt.org> |
| Futurepedia | <https://futurepedia.io> |
| There's An AI For That | <https://theresanaiforthat.com> |
| AlternativeTo | <https://alternativeto.net> |
| SaaSHub | <https://www.saashub.com> |
| StartupBase | <https://startupbase.io> |
| Launching Next | <https://launchingnext.com> |
| G2 | <https://www.g2.com> |
| Capterra | <https://www.capterra.com> |
| GetApp | <https://www.getapp.com> |
| TrustRadius | <https://www.trustradius.com> |
| GoodFirms | <https://www.goodfirms.co> |
| Software Advice | <https://www.softwareadvice.com> |
| Crunchbase | <https://www.crunchbase.com> |

### Tier 6 — Mobile & ASO directories

| Directory | URL |
| --- | --- |
| AppRadar Blog | <https://appradar.com/blog> |
| Checkaso | <https://checkaso.com> |
| Mobile Action | <https://www.mobileaction.co> |
| Toolfolio | <https://toolfolio.io> |
| LaunchShots Blog | <https://www.launchshots.com/blog> |
| AppDrift | <https://appdrift.co> |
| NeoAds | <https://neoads.tech> |

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
