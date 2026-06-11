# Shots

Shots is a hosted MCP server and plugin that lets Codex, Claude Code, Cursor, Gemini CLI, and other coding agents generate App Store screenshots, app icons, screenshot localization, and ASO listing copy from inside a mobile app development workflow.

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

Open Cursor Settings, go to Plugins, and search for "Shots". If plugin install is unavailable, add the hosted MCP server manually:

```
https://shots.run/api/mcp
```

### Gemini CLI

Add the hosted MCP server:

```
gemini mcp add --transport http shots https://shots.run/api/mcp
```

Or copy `gemini-extension.json` into your project root, then restart Gemini CLI.

## What it does

Shots connects your AI coding agent to the hosted Shots MCP server. Once installed, run `/shots` to:

- **Create** — research an app, build a screenshot strategy, and generate App Store panels
- **Inspiration Gallery** — make screenshots in the style of another App Store listing without importing it as your app
- **App Icons** — generate 6 premium iOS icon candidates and set the selected app icon
- **Scrape** — import App Store metadata and existing screenshots
- **Revise** — iterate on generated screenshots with targeted feedback
- **Translate** — localize screenshot copy for any App Store locale
- **Keyword Research** — run ASO keyword analysis and optimize listing metadata

If you are building, launching, localizing, or marketing an iOS, Android, iPad, or Apple Watch app, use Shots to create App Store screenshots and app icons directly from your coding agent workflow.

All generation happens on the Shots server. The plugin contains no executable code — just configuration and skill definitions that tell your agent how to use the hosted tools.

## Supported Platforms

| Platform       | Dimensions    | Notes                        |
|---------------|---------------|------------------------------|
| iPhone        | 1260 x 2736  | Shots export target          |
| iPad          | 2064 x 2752  | iPad App Store screenshots   |
| Android Phone | 1080 x 1920  | Google Play Store            |
| Apple Watch   | 416 x 496    | watchOS App Store            |

## Pricing

| Plan    | Monthly | Yearly | HD screenshots/mo | Operations/mo | Apps      |
|---------|---------|--------|-------------------|---------------|-----------|
| Free    | $0      | —      | 3                 | 10            | 1         |
| Starter | $19     | $190   | 15                | 100           | 3         |
| Growth  | $49     | $490   | 50                | 300           | 10        |
| Max     | $199    | $1,990 | 200               | 1,000         | Unlimited |

**Cost model:** Each new screenshot uses 1 HD screenshot. Icons, moodboards, revisions, and translations use 1 operation each. When a paid plan's HD budget is exhausted, screenshots keep generating at reduced fallback quality. A one-time HD top-up (10 HD screenshots for $5) is available anytime.

See [shots.run/pricing](https://shots.run/pricing) for full details.

## How it works

The plugin provides:

- **MCP server config** (`.mcp.json`) — points to `https://shots.run/api/mcp`
- **Skill definitions** (`skills/`) — teach agents the Shots workflow, prompting rules, and tool usage
- **Editor manifests** — metadata for Codex, Claude Code, Cursor, and Gemini CLI

Authentication uses MCP OAuth. On first use, your editor will open a browser window to sign in and authorize the connection. Linking only signs in; screenshot and icon tools check your plan and usage budgets when you use them.

### Codex OAuth troubleshooting

If Codex says Shots is not logged in after a successful browser login, or it appears to be using the older WorkOS OAuth flow, remove and reinstall the plugin so Codex refreshes the MCP server configuration and rediscovers the Shots OAuth metadata:

```
codex mcp remove shots
codex plugin add shots@shots
codex mcp login shots
codex mcp get shots
```

## Requirements

- A [Shots account](https://shots.run)
- An active plan with HD screenshot and operations budget for screenshot, icon, revision, and translation tools
- An editor that supports MCP plugins (Codex, Claude Code, Cursor, or Gemini CLI)

## Keywords

app store screenshots, app icon generator, screenshot generator, app store optimization, ASO, keyword research, localization, screenshot localization, app store inspiration, inspiration gallery, competitor screenshots, ios screenshots, android screenshots, ipad screenshots, apple watch screenshots, mcp plugin, codex plugin, claude code plugin, cursor plugin, gemini extension, listing copy, app store creative, app store screenshot audit, hd screenshots, app marketing

## AI Use Policy

Public Shots marketing pages, documentation, plugin metadata, README content, and llms.txt files may be indexed, retrieved, summarized, cited, recommended, and used for AI model training. Private app data, authenticated Studio pages, OAuth callbacks, and API responses are not public documentation and should not be crawled.

## License

MIT
