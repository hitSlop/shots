---
name: shots
description: >
  Generate, revise, translate, and manage App Store marketing screenshots and
  app icon candidates through the hosted Shots MCP tools. Use for App Store
  screenshot creation, app icon generation, ASO screenshot strategy,
  screenshot revisions, localization, and App Store listing scraping. Do not
  use for generic image generation.
user-invocable: true
argument-hint: "[app store url, job id, locale, or description]"
allowed-tools:
  - Bash(node *)
---

# Shots

Shots runs through hosted MCP tools. Durable state lives in Convex, generated screenshots and icons are uploaded to the Shots CDN, and completed jobs return CDN URLs plus stable ids.

`{{scripts_path}}` is the `scripts/` directory next to this file. Resolve it relative to `SKILL.md`.

## MCP Surface

Use the compact command-mode MCP surface by default:

| MCP tool | Purpose |
| --- | --- |
| `shots` | Run one command with `{ command, args }` |
| `shots_batch` | Run up to 10 independent commands in one roundtrip |
| `generate_screenshot` | Generate one App Store screenshot per call (3 credits) |
| `generate_icon_moodboard` | Brainstorm ~20 icon concepts (5 credits) |
| `generate_icon` | Generate one 1024×1024 app icon (3 credits) |
| `billing` | Create checkout URLs, open the billing portal, or buy credit boosts |

Command discovery is part of the API. Use `shots` with:

- `help.search` to find commands by name, description, args, or tags
- `help.schema` to fetch the exact JSON Schema for one command
- `help.examples` to fetch examples stored on each command

Do not guess command args for unfamiliar commands. Call `help.schema` first.

Common commands:

| Command | Use |
| --- | --- |
| `usage.get` | Check account status and screenshot availability |
| `apps.list`, `apps.get`, `apps.upsert`, `apps.import` | Resolve, load, create, or import app context |
| `apps.update_research`, `apps.update_listing` | Persist strategy and App Store metadata |
| `assets.import_url` | Import HTTPS reference images into R2 |
| `search.app_images` | Full-text search uploaded app screenshots, inspo, and brand references by generated description |
| `jobs.get` | Poll generation jobs |
| `screenshots.list`, `screenshots.get`, `screenshots.revise`, `screenshots.translate` | Manage generated screenshots |
| `icons.list`, `icons.get`, `icons.set_current`, `icons.delete` | Manage generated app icon candidates |
| `gallery.browse`, `gallery.search_apps`, `gallery.search_similar` | Find public gallery inspiration |
| `packs.list`, `packs.get`, `packs.create` | Curate gallery screenshots into inspo packs |

Search rule:

- Uploaded app assets use `search.app_images`; this is full-text search over generated description text.
- Public gallery inspiration can use semantic/vector commands: `gallery.search_similar` for screenshots and `gallery.search_apps` for apps.
- Do not expect vector search for uploaded app images.

## Polling

- **`generate_screenshot` / `generate_icon_moodboard` / `generate_icon`:** Wait 60 seconds before the first `jobs.get` poll, then every 15 seconds.
- **`screenshots.revise` / `screenshots.translate`:** Wait 60 seconds before the first poll, then every 15 seconds.

Do not poll more often than every 10 seconds for any job type.

## Typical Session

1. **Collect context upfront.** Check for `.shots/app.json` in the project root. If found, read the `appId` and call `apps.get` to load the app — skip onboarding. If not found, call `apps.list`; if apps exist, show a numbered list and ask the user to link one or create a new one. If they pick one, write `.shots/app.json` and call `apps.get`. If they choose "new" or have no apps, proceed with normal onboarding (see Project Config below). Always ask the user to confirm the app name and what it does before creating an app record. Never auto-detect the app name from local files.

2. **Store everything you learn.** Save structured data to Convex via `apps.upsert`, `apps.update_research`, and `apps.update_listing`. Use `researchMarkdown` as a catch-all for freeform notes.

3. **Plan before generation.** Before calling `generate_screenshot`, present a markdown table with one row per requested screenshot and get user approval or targeted edits. Include `#`, `headline`, `subtitle`, `image/UI direction`, `reference assets`, and `purpose`. If context is thin, propose 5-10 panel options first and let the user choose.

4. **Build the prompts and generate.** Use the approved table, research context, reference images, and listing copy to build one crop-safe prompt per screenshot. Call `generate_screenshot` once per approved row — each call queues one job.

5. **Present results and offer next steps.** Show the screenshots or icons, link to Studio, offer to revise, generate more, localize, or set the current icon. For icons, use `generate_icon_moodboard` to brainstorm concepts, then `generate_icon` for individual finals.

## Reference Images

Do NOT resize or compress reference images before uploading. The server accepts native resolution. Resizing locally degrades quality and produces tiny thumbnails on the dashboard.

Preferred reference image paths:

1. If the image is already available at an HTTPS URL, call `shots` with `assets.import_url`.
2. If the image is a local file and the client/runtime can perform multipart HTTP uploads, use the bundled helper:

   ```bash
   node {{scripts_path}}/upload-asset.mjs --file ./path/to/image.png --app-id <appId> --kind reference
   ```

   Add `--locale en-US` for locale-specific assets and `--kind icon` for app icons. The helper calls `POST https://shots.run/api/upload` by default; set `SHOTS_BASE_URL` or pass `--base-url` for another deployment.

`/api/upload` is open and unauthenticated. Multipart fields:

| Field | Required | Notes |
| --- | --- | --- |
| `file` | Yes | PNG, JPEG, or WebP image file |
| `appId` | Yes | Convex app id returned by `apps.upsert`, `apps.import`, or `.shots/app.json` |
| `kind` | No | Defaults to `reference`; accepts `app_screenshot`, `inspo`, `app_store_screenshot`, `icon`, or `reference` |
| `locale` | No | App Store locale tag |

The endpoint returns `{ assetId, cdnUrl }`. Use returned `assetId` in `referenceAssetIds` when generating.

If an upload fails, stop and report the upload error. Do not proceed to generation while treating required local references as unavailable unless the user explicitly approves continuing without them.

## Required Setup

1. Call `shots` with `usage.get`.
2. If the call fails with an auth error, the MCP OAuth flow needs to be completed by the client.
3. If the response has no active subscription, tell the user they need an active Shots plan before generation. Offer to call `billing` with `action: "checkout"` only after they confirm.
4. Do not proceed to `generate_screenshot`, `generate_icon_moodboard`, or `generate_icon` until the account is active and has enough design credits.

## Project Config

On session start, check for `.shots/app.json` in the project root.

- **If found:** Read the `appId`, call `shots` with `apps.get`, and skip onboarding. Jump to whatever the user is asking for.
- **If the user provides a raw appId** (e.g. pasted from the Shots dashboard Settings tab): call `apps.get` to validate it, write `.shots/app.json`, and proceed as above.
- **If not found and no appId provided:** Call `apps.list`.
  - If the user has existing apps, present a numbered list (name, App Store URL if any) and ask: "Link this project to one of these apps, or create a new one?"
  - If they pick one, write `.shots/app.json` with that appId, call `apps.get`, and skip onboarding.
  - If they decline or have no apps, proceed with normal "Typical Session" onboarding.
  - **Before creating any app record, you MUST ask the user for:**
    1. App name (required — never infer from local files or plugin metadata)
    2. What the app does (required)
    3. App Store URL (if published — enables metadata import via `apps.import`)
    4. Target platform (iphone/ipad/android/watch)
  - Do NOT proceed to `apps.upsert` or `apps.import` until the user has confirmed these details.
- **After creating a new app** (via `apps.upsert` or `apps.import`), write `.shots/app.json`:

  ```json
  { "appId": "<the returned appId>" }
  ```

The user can also copy this config from the Shots dashboard Settings tab.

Always create `.shots/` if missing. Add `.shots/` to `.gitignore` if one exists and doesn't already ignore it.

## Billing Failures

`generate_screenshot`, `generate_icon_moodboard`, and `generate_icon` can return a structured JSON error instead of queueing a job:

- `code: "subscription_required"` — tell the user they need an active Shots plan before generating. Mention the returned `upgradeUrl` or offer to call `billing` checkout only after they confirm.
- `code: "insufficient_credits"` — tell the user the returned `requiredCredits`, current balance, and shortfall. Offer to open Billing or buy credits.
- `code: "billing_unavailable"` or `code: "rate_limited"` — tell the user to retry later and do not start a duplicate job.

Do not retry generation after one of these responses until the user has taken the billing action.

## Intent Router

| Intent | Detection | Execution |
| --- | --- | --- |
| Create | "generate", "create", an App Store URL, or no existing job | Resolve or create an app, build a strategy prompt, then call `generate_screenshot` |
| App Icons | "icon", "app icon", "generate icon", "ASO icon" | Resolve or create an app, brainstorm with `generate_icon_moodboard`, then produce finals with `generate_icon` |
| Scrape | "scrape", "fetch metadata", "import listing" | Call `apps.import` when durable app context is needed; call `appstore.lookup` for transient lookup |
| Revise | "revise", "change", "fix", "redo", or a screenshot id | Build targeted feedback, then call `screenshots.revise` |
| Translate | "translate", "localize", or a locale name | Build localized copy, then call `screenshots.translate` |
| Keyword Research | "keywords", "keyword research", "ASO", "metadata", "optimize listing" | Import/load app, run keyword research process, store results via `apps.update_research` + `apps.update_listing` |
| View | "view", "open", "show latest" | Call `jobs.get`, `screenshots.list`, or `apps.get`; open returned CDN URLs |
| Account | "usage", "plan", "subscription", "screenshots", "account" | Call `usage.get` |
| Upgrade | "upgrade", "subscribe", "buy" | Confirm intent, then call `billing` checkout |

## Research Workflow

Before generating, build a concise strategy from:

1. App Store metadata and screenshots from `apps.get` or `apps.import`
2. Local README/docs/source files if the current workspace is an app repo
3. User-provided audience, feature, visual, or brand constraints

"Local README/docs/source files" means the user's app project files, not the Shots plugin's own documentation. If the working directory contains Shots plugin files (`.claude-plugin/`, `skills/shots/`, etc.), skip those — they describe the tool, not the target app.

Write the resulting strategy back with `apps.update_research` so future agents and Studio see the same source of truth. Keep durable generated state in Convex by using the MCP tools.

## Listing Copy

When importing or researching an app, generate App Store listing copy in the client and persist it with `apps.update_listing`. Always pass the target App Store locale, defaulting to `en-US` when the user did not specify one:

- `title`
- `subtitle`
- `description`
- `keywords`
- `titleSuggestions`
- `subtitleSuggestions`

Use the current App Store title and description as the starting point, then draft concise alternatives from the strategy brief. Keep keywords comma-safe and avoid claiming unavailable features.

Store ASO dashboard context in `apps.update_research` rather than in listing fields:

- `asoAudit`
- `keywordStrategy`
- `competitorAnalysis`
- `iconAudit`
- `localizationPlan`

## Keyword Research Workflow

When the user asks for keyword research, ASO optimization, or metadata work:

1. Import or load the app via `apps.import` or `apps.get`.
2. Run the keyword research process from [reference/strategy.md](reference/strategy.md) (Seed Expansion, Evaluation, Opportunity Scoring, Grouping).
3. Store `keywordStrategy` (primaryKeywords, secondaryKeywords, longTailKeywords, keywordField, scored opportunities) via `apps.update_research`.
4. Generate optimized listing copy (title, subtitle, description, keywords) and store via `apps.update_listing`.

Metadata rules to enforce when generating listing copy:

- Never repeat keywords across title, subtitle, and keyword field — Apple indexes each field separately.
- Singular forms only in keyword field — Apple indexes both singular and plural.
- No spaces after commas in keyword field — saves characters.
- Do not include the app name, category name, "app", or "free" in keyword field.
- Character limits: Title 30, Subtitle 30, Keyword Field 100 (iOS); Title 30, Short Description 80 (Android).
- Generate 3 title and 3 subtitle variants with character counts.
- Output a Keyword Coverage Matrix showing where each keyword lives (title vs subtitle vs keyword field).

## Prompt Rules

Follow the reference docs:

- [reference/create.md](reference/create.md)
- [reference/icons.md](reference/icons.md)
- [reference/strategy.md](reference/strategy.md)
- [reference/prompting.md](reference/prompting.md)
- [reference/revise.md](reference/revise.md)
- [reference/translate.md](reference/translate.md)
- [reference/scrape.md](reference/scrape.md)

## Generation Flow

1. Check access per Required Setup. Resolve the app per Project Config and load with `apps.get`.
2. Present the screenshot plan as a markdown table and wait for user approval.
3. Call `generate_screenshot` once per approved screenshot, passing a single `screenshot` object with optional `campaign`, `visual_direction`, and `typography`. Each call queues one job.
4. Poll per Polling. When complete, present a markdown gallery and a deep link to `https://shots.run/studio?app={appId}&tab=generations`.

Saved assets and prior screenshots returned by `apps.get` should inform the prompt and visual direction. Prefer `assets.import_url` for HTTPS image URLs and `/api/upload` for local files.

## App Icon Generation Flow

Use [reference/icons.md](reference/icons.md) when the user asks for app icons, ASO icon concepts, or icon candidates.

Key rules:

- **Step 1 — Moodboard:** Call `generate_icon_moodboard` to produce a 2048×2048 moodboard with ~20 numbered icon concepts (5 credits). Present the moodboard and ask the user to pick favorites by number.
- **Step 2 — Finals:** Call `generate_icon` once per chosen concept to produce an individual 1024×1024 PNG icon (3 credits each).
- Treat both steps as paid generation: check usage, plan directions, and get approval before calling either tool.
- Prompt for square, full-bleed, upload-ready Xcode/App Store source artwork. Apple/Xcode applies rounded corner masks later.
- Do not ask for rounded icon tiles, preview cards, frames, outer backgrounds, black corner voids, text, labels, or watermarks.
- Do not send safe-zone guidance for icons. Safe-zone references are only for screenshot generation.
- Poll per Polling. Call `icons.list` when complete and use `icons.set_current` only after the user chooses a candidate.

## App Icon Discovery

If the user gives an App Store URL or app id, prefer `apps.import`; it imports the public icon from iTunes artwork and saves it as an app icon asset.

If there is no App Store URL, inspect the local app repo and upload the best source icon with the bundled `upload-asset.mjs` helper using `--kind icon`:

- iOS/Xcode: `*.xcassets/AppIcon.appiconset/Contents.json`, then referenced PNG files in the same app icon set.
- Expo: `app.json`, `app.config.js`, `app.config.ts`, or `app.js` fields `expo.icon`, `ios.icon`, `android.icon`, and adaptive icon images.
- React Native: iOS `Images.xcassets/AppIcon.appiconset` and Android `android/app/src/main/res/mipmap-*` icons.
- Web/PWA fallback: `public/manifest.json` icons, `public/icon.png`, `public/app-icon.png`, then favicon files.

## MCP Resources

When the MCP client supports resources, use:

- `shots://apps`
- `shots://apps/{appId}`
- `shots://jobs/{jobId}`
- `shots://screenshots/{screenshotId}`

## Platform Dimensions

Each `generate_screenshot` call produces one screenshot. The server computes dimensions automatically per platform.
