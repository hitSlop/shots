# Shots

Generate, revise, translate, and manage App Store marketing screenshots and app icon candidates through hosted MCP tools.

**[shots.run](https://shots.run)**

## Install

### Codex

Add the marketplace:

```
codex plugin marketplace add hitSlop/shots
```

Install the plugin from inside Codex:

```
codex
# Then run /plugins, select Shots, and install
/plugins
```

Or install directly from the command line:

```
codex plugin add shots@shots
```

### Claude Code

```
claude plugin install hitSlop/shots
```

### Cursor

Settings → Plugins → search "Shots"

### Gemini CLI

Copy `gemini-extension.json` into your project root, then restart Gemini CLI.

## What it does

Shots connects your AI coding agent to the hosted Shots MCP server. Once installed, run `/shots` to:

- **Create** — research an app, build a screenshot strategy, and generate App Store panels
- **App Icons** — generate 6 premium iOS icon candidates and set the selected app icon
- **Scrape** — import App Store metadata and existing screenshots
- **Revise** — iterate on generated screenshots with targeted feedback
- **Translate** — localize screenshot copy for any App Store locale
- **Keyword Research** — run ASO keyword analysis and optimize listing metadata

All generation happens on the Shots server. The plugin contains no executable code — just configuration and skill definitions that tell your agent how to use the hosted tools.

## How it works

The plugin provides:

- **MCP server config** (`.mcp.json`) — points to `https://shots.run/mcp`
- **Skill definitions** (`skills/`) — teach agents the Shots workflow, prompting rules, and tool usage
- **Editor manifests** — metadata for Codex, Claude Code, Cursor, and Gemini CLI

Authentication uses MCP OAuth. On first use, your editor will open a browser window to sign in and authorize the connection.

## Requirements

- An active [Shots subscription](https://shots.run/pricing)
- An editor that supports MCP plugins (Codex, Claude Code, Cursor, or Gemini CLI)

## License

MIT
