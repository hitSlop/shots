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

## Supported Platforms

| Platform       | Dimensions    | Notes                        |
|---------------|---------------|------------------------------|
| iPhone 6.9"   | 1320 x 2868  | Required for App Store       |
| iPad 13"      | 2064 x 2752  | Required for iPad apps       |
| Android Phone | 1080 x 1920  | Google Play Store            |
| Apple Watch   | 410 x 502    | watchOS App Store            |

## Pricing

| Plan      | Monthly | Yearly (equiv.) | Credits/mo | Apps      | Screenshots/mo | Icons/mo |
|-----------|---------|-----------------|------------|-----------|----------------|----------|
| Free      | $0      | —               | 10 (once)  | 1         | 3              | 3        |
| Starter   | $12     | $10/mo          | 60         | 5         | 15             | 20       |
| Pro       | $39     | $32.50/mo       | 200        | 15        | 50             | 66       |
| Unlimited | $149    | $108.25/mo      | Unlimited  | Unlimited | Fair use       | Fair use |

**Credit costs:** Screenshot (4), App icon (3), Icon moodboard (5), Revision/translation (1)

See [shots.run/pricing](https://shots.run/pricing) for full details.

## How it works

The plugin provides:

- **MCP server config** (`.mcp.json`) — points to `https://shots.run/mcp`
- **Skill definitions** (`skills/`) — teach agents the Shots workflow, prompting rules, and tool usage
- **Editor manifests** — metadata for Codex, Claude Code, Cursor, and Gemini CLI

Authentication uses MCP OAuth. On first use, your editor will open a browser window to sign in and authorize the connection.

## Requirements

- An active [Shots subscription](https://shots.run/pricing)
- An editor that supports MCP plugins (Codex, Claude Code, Cursor, or Gemini CLI)

## Keywords

app store screenshots, app icon generator, screenshot generator, app store optimization, ASO, localization, ios screenshots, android screenshots, ipad screenshots, apple watch screenshots, mcp plugin, codex plugin, claude code plugin, cursor plugin, gemini extension, listing copy, app store creative, screenshot translation, app store localization, design credits, app marketing

## License

MIT
