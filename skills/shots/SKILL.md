---
name: shots
description: >
  Generate, revise, translate, and manage App Store marketing screenshots
  through the hosted Shots MCP tools. Use for App Store screenshot creation,
  ASO screenshot strategy, screenshot revisions, localization, and App Store
  listing scraping. Do not use for generic image generation.
user-invocable: true
argument-hint: "[app store url, job id, locale, or description]"
---

# Shots

Shots runs through hosted MCP tools. Durable state lives in Convex, generated
screenshots are uploaded to the Shots CDN, and completed jobs return CDN URLs
plus stable screenshot ids.

## Tools

| Tool | Purpose |
| --- | --- |
| `get_usage` | Check plugin connectivity, plan status, and remaining screenshots |
| `lookup_app_store_listing` | Fetch public iTunes metadata and screenshot groups |
| `list_apps` | List existing app records in Convex |
| `upsert_app` | Create or update an app record |
| `get_app_context` | Fetch app metadata, locale listings, R2 assets, research, screenshots, and jobs |
| `import_app_store_listing` | Save App Store metadata, icon, and screenshots to an app |
| `update_app_store_listing` | Save locale-specific App Store title, subtitle, description, keywords, and suggestion arrays |
| `upload_app_asset` | Save user-provided app screenshots, inspiration, icons, or references to R2, optionally tagged by locale |
| `import_app_asset_from_url` | Fetch an HTTPS image URL, save it to R2, and attach it to an app, optionally tagged by locale |
| `update_app_research` | Write structured research back to the app |
| `generate_screenshots` | Submit a hosted screenshot generation job, optionally linked to an app |
| `get_job` | Poll job status, CDN asset URLs, and generated screenshot ids |
| `list_screenshots` | List generated screenshot rows with stable ids |
| `get_screenshot` | Fetch one generated screenshot row |
| `upgrade_plan` | Get a checkout URL after the user confirms they want to subscribe |
| `cancel_plan` | Get the billing portal URL for plan management |

## Typical Session

1. **Collect context upfront.** First check for `.shots/app.json` — if it
   exists, load the app with `get_app_context` and skip to step 3. Otherwise
   ask the user:
   - App name
   - What it does / what problem it solves (1-2 sentences)
   - App Store URL (if published — enables auto-import of metadata, icon,
     screenshots)
   - How many screenshots they want (default: 3)
   - Any visual inspiration — competitor URLs, existing screenshots, brand
     guidelines
   - Target platform (iPhone / iPad / Android, default: iPhone)

   Ask these as a batch, not one-by-one. If the user provides an App Store URL
   or the workspace is an app repo, you can infer most of these.

2. **Store everything you learn.** Save structured data to Convex via
   `upsert_app`, `update_app_research`, and `update_app_store_listing`.
   Use `researchMarkdown` as a catch-all for freeform notes.

3. **Build the prompt and generate.** Use the research context, reference
   images, and listing copy to build the generation prompt. Call
   `generate_screenshots`.

4. **Set timing expectations.** Tell the user generation takes 1-2 minutes.
   Wait 60 seconds before the first poll, then poll `get_job` every 15 seconds.
   Don't poll more often than every 10 seconds.

5. **Present results and offer next steps.** Show the panels, link to Studio,
   offer to revise, generate more, or localize.

## Reference Images

Do NOT resize or compress reference images before uploading. The server accepts
native resolution. Resizing locally degrades quality and produces tiny thumbnails
on the dashboard. Upload images as-is via `upload_app_asset` or
`import_app_asset_from_url`.

## Required Setup

1. Call `get_usage`.
2. If the call fails with an auth error, the MCP OAuth flow needs to be
   completed by the client.
3. If the response has no active subscription, tell the user they need an
   active Shots plan before generation. Offer to call `upgrade_plan` only after
   they confirm.
4. Do not proceed to `generate_screenshots` until the account is active and has
   available screenshots, fair-use access, or accepted overage.

## Project Config

On session start, check for `.shots/app.json` in the project root.

- **If found:** Read the `appId`, call `get_app_context`, and skip the onboarding
  questions in "Typical Session" step 1. Jump directly to whatever the user is
  asking for (generate, revise, translate, etc.).
- **If not found:** Run the normal "Typical Session" flow. After creating the app
  (via `upsert_app` or `import_app_store_listing`), write `.shots/app.json` to
  the project root:

  ```json
  {
    "appId": "<the returned appId>"
  }
  ```

Always create the `.shots/` directory if it doesn't exist. Add `.shots/` to the
project's `.gitignore` if a `.gitignore` exists and doesn't already ignore it.

## Billing Failures

`generate_screenshots` can return a structured JSON error instead of queueing a
job:

- `code: "subscription_required"` — tell the user they need an active Shots
  plan before generating. Mention the returned `upgradeUrl` or offer to call
  `upgrade_plan` only after they confirm.
- `code: "credits_exhausted"` with `overageAllowed: false` — tell the user they
  are out of screenshots and need to upgrade or wait for reset.
- `code: "credits_exhausted"` with `overageAllowed: true` — tell the user this
  generation needs extra usage acceptance. Ask before opening the returned
  `acceptOverageUrl` or billing flow.

Do not retry generation after one of these responses until the user has taken
the billing action.

## Intent Router

| Intent | Detection | Execution |
| --- | --- | --- |
| Create | "generate", "create", an App Store URL, or no existing job | Resolve or create an app, build a strategy prompt, then call `generate_screenshots` |
| Scrape | "scrape", "fetch metadata", "import listing" | Call `import_app_store_listing` when durable app context is needed; call `lookup_app_store_listing` for transient lookup |
| Revise | "revise", "change", "fix", "redo", or a job id | Build a targeted revision prompt, then call `generate_screenshots` |
| Translate | "translate", "localize", or a locale name | Build localized copy, then call `generate_screenshots` |
| Keyword Research | "keywords", "keyword research", "ASO", "metadata", "optimize listing" | Import/load app, run keyword research process, store results via `update_app_research` + `update_app_store_listing` |
| View | "view", "open", "show latest" | Call `get_job`, `list_screenshots`, or `get_app_context`; open returned CDN URLs |
| Account | "usage", "plan", "subscription", "screenshots", "account" | Call `get_usage` |
| Upgrade | "upgrade", "subscribe", "buy" | Confirm intent, then call `upgrade_plan` |

## Research Workflow

Before generating, build a concise strategy from:

1. App Store metadata and screenshots from `get_app_context` or `import_app_store_listing`
2. Local README/docs/source files if the current workspace is an app repo
3. User-provided audience, feature, visual, or brand constraints

Write the resulting strategy back with `update_app_research` so future agents
and Studio see the same source of truth. Keep durable generated state in Convex
by using the MCP tools.

## Listing Copy

When importing or researching an app, generate App Store listing copy in the
client and persist it with `update_app_store_listing`. Always pass the target
App Store locale, defaulting to `en-US` when the user did not specify one:

- `title`
- `subtitle`
- `description`
- `keywords`
- `titleSuggestions`
- `subtitleSuggestions`

Use the current App Store title and description as the starting point, then
draft concise alternatives from the strategy brief. Keep keywords comma-safe
and avoid claiming unavailable features.

Store ASO dashboard context in `update_app_research` rather than in listing
fields:

- `asoAudit`
- `keywordStrategy`
- `competitorAnalysis`
- `iconAudit`
- `localizationPlan`

## Keyword Research Workflow

When the user asks for keyword research, ASO optimization, or metadata work:

1. Import or load the app via `import_app_store_listing` or `get_app_context`.
2. Run the keyword research process from
   [reference/strategy.md](reference/strategy.md) (Seed Expansion, Evaluation,
   Opportunity Scoring, Grouping).
3. Store `keywordStrategy` (primaryKeywords, secondaryKeywords, longTailKeywords,
   keywordField, scored opportunities) via `update_app_research`.
4. Generate optimized listing copy (title, subtitle, description, keywords) and
   store via `update_app_store_listing`.

Metadata rules to enforce when generating listing copy:

- Never repeat keywords across title, subtitle, and keyword field — Apple
  indexes each field separately.
- Singular forms only in keyword field — Apple indexes both singular and plural.
- No spaces after commas in keyword field — saves characters.
- Do not include the app name, category name, "app", or "free" in keyword field.
- Character limits: Title 30, Subtitle 30, Keyword Field 100 (iOS); Title 30,
  Short Description 80 (Android).
- Generate 3 title and 3 subtitle variants with character counts.
- Output a Keyword Coverage Matrix showing where each keyword lives (title vs
  subtitle vs keyword field).

## Prompt Rules

Follow the reference docs:

- [reference/create.md](reference/create.md)
- [reference/strategy.md](reference/strategy.md)
- [reference/prompting.md](reference/prompting.md)
- [reference/revise.md](reference/revise.md)
- [reference/translate.md](reference/translate.md)
- [reference/scrape.md](reference/scrape.md)

## Generation Flow

1. Call `get_usage`.
2. Call `list_apps`, `upsert_app`, or `import_app_store_listing` to establish
   the app record when the user is working on a specific app. Pass a locale
   when the user is working outside the app's primary App Store locale.
3. Call `get_app_context` and use saved R2 assets, generated screenshot ids,
   locale listings, and the app research object as context.
4. Update listing copy with `update_app_store_listing` when the user asks for
   ASO metadata, import cleanup, localization, or title/subtitle alternatives.
5. Build the final prompt using the approved strategy and visible screenshot
   copy.
6. Call `generate_screenshots` with `appId`, `locale`, `prompt`, `platform`,
   `panelCount`, and `quality`.
7. Wait 60 seconds before the first `get_job` poll, then poll every 15 seconds
   until status is `"complete"` or `"failed"`.
8. When complete, present screenshots side by side using the HTML table gallery
   format described in [reference/create.md](reference/create.md) step 6, with
   a deep link to `https://shots.run/studio?app={appId}&tab=generations`.

Saved assets and prior screenshots returned by `get_app_context` should inform
the prompt and visual direction. Prefer `import_app_asset_from_url` for normal
image URLs and `upload_app_asset` only for small inline/base64 images.

## App Icon Discovery

If the user gives an App Store URL or app id, prefer `import_app_store_listing`;
it imports the public icon from iTunes artwork and saves it as an app icon
asset.

If there is no App Store URL, inspect the local app repo and upload the best
source icon with `upload_app_asset` using `kind: "icon"`:

- iOS/Xcode: `*.xcassets/AppIcon.appiconset/Contents.json`, then referenced
  PNG files in the same app icon set.
- Expo: `app.json`, `app.config.js`, `app.config.ts`, or `app.js` fields
  `expo.icon`, `ios.icon`, `android.icon`, and adaptive icon images.
- React Native: iOS `Images.xcassets/AppIcon.appiconset` and Android
  `android/app/src/main/res/mipmap-*` icons.
- Web/PWA fallback: `public/manifest.json` icons, `public/icon.png`,
  `public/app-icon.png`, then favicon files.

## MCP Resources

When the MCP client supports resources, use:

- `shots://apps`
- `shots://apps/{appId}`
- `shots://jobs/{jobId}`
- `shots://screenshots/{screenshotId}`

## Platform Dimensions

| Platform | Final screenshot |
| --- | --- |
| iphone | 1284x2778 |
| ipad | 2048x2732 |
| android | 1080x1920 |
