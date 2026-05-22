# Shots Create Flow

Create a new hosted screenshot set from app context, App Store metadata, and the user's positioning goals.

## Steps

### 1. Check Access

Call `shots` with `usage.get` before doing generation work. If the account is inactive or out of usable screenshots, stop and ask whether the user wants a subscription link. Only call `billing` checkout after they confirm.

### 2. Gather Inputs

First check for `.shots/app.json` in the project root. If it contains an `appId`, call `shots` with `apps.get` to load the existing app and skip the onboarding questions below.

If the user provides a raw appId (e.g. pasted from the Shots dashboard), call `apps.get` to validate it, write `.shots/app.json`, and skip onboarding.

If no config exists and no appId was provided, call `apps.list`. If apps are returned, present a numbered list and ask which to link — or create new. If they pick one, write `.shots/app.json` and load with `apps.get`.

If the user provides an App Store URL or app id, call `apps.import` to create or update the durable app record and save the public icon/screenshots to R2. Pass the target locale when known. If the user already has an app record, call `apps.get`. Also inspect local README/docs/source files if the current workspace is the app repo.

If there is no App Store URL, find an app icon in the local repo before generation when possible. Check Xcode app icon sets, Expo config icon fields, React Native iOS/Android icons, then PWA/public icon fallbacks. Upload it with the bundled helper when the client/runtime can upload local files:

```bash
node {{scripts_path}}/upload-asset.mjs --file ./path/to/icon.png --app-id <appId> --kind icon
```

Use `assets.import_url` for HTTPS image URLs.

If any required local reference upload fails, stop and report the exact upload error. Do not continue to `generate_screenshots` without those references unless the user explicitly approves generating without them.

Minimum signal:

- `appName` or a clearly identified product
- app description or category
- at least one visual cue: App Store screenshots, app icon, brand colors, or existing product UI in the repo

Ask only for missing information that would materially change the panels.

### 3. Build Strategy

Follow [strategy.md](strategy.md). Summarize:

- one-line positioning
- target audience
- visual theme and palette
- 3-5 market words
- first 6-8 benefits

Get approval or targeted edits before generation.

Save approved strategy back to the app with `apps.update_research`.

### 4. Present Screenshot Plan

Before generation, show a markdown table with one row per requested screenshot. Do not call `generate_screenshots` until the user approves or edits the table.

```markdown
| # | Headline | Subtitle | Image/UI direction | Reference assets | Purpose |
| --- | --- | --- | --- | --- | --- |
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |
```

If the user asks for a specific count, plan exactly that count. The server will split submitted screenshots into jobs according to the platform limits:

- **iPhone:** up to 3 panels per job
- **iPad:** up to 2 panels per job
- **Android:** up to 3 panels per job
- **Watch:** up to 4 panels per job (2×2 grid)

If there is not enough context to choose the panels confidently, propose 5-10 candidate panel concepts in the same table format and ask the user which ones to generate.

### 5. Build Prompt

Follow [prompting.md](prompting.md). Expand each approved benefit into a structured panel: `headline`, `subtitle`, `role`, `layout`, `device`/`devices` with specific screen content, `background`, and optional `breakout_elements` and `references` (mapping reference images by index to usage instructions).

### 6. Generate

Call `generate_screenshots` with structured `panels` and optional `campaign`, `visual_direction`, and `typography`. The server injects dimensions, format, app context, reference image metadata, safe-zone guidance, and constraints — do not send these fields.

```json
{
  "appId": "APP_ID",
  "platform": "iphone",
  "quality": "high",
  "referenceAssetIds": ["ASSET_ID"],
  "campaign": {
    "goal": "Drive downloads from App Store search",
    "audience": "Men 22-38 wanting better dating photos",
    "core_promise": "Dating-specific AI photos that look real",
    "differentiator": "37 themed photo scenes, not generic AI headshots"
  },
  "visual_direction": {
    "style_family": "clean-premium",
    "mood": "dark, confident, aspirational",
    "palette": { "primary": "#0F1416", "accent": "#119DA4", "text": "#FFFFFF" }
  },
  "panels": [
    {
      "headline": "37 Scenes. Pick Your Story.",
      "subtitle": "Dinner. Travel. Fitness. More.",
      "role": "Show variety of photo packs",
      "device": { "model": "iPhone 15 Pro", "screen": "Browse scenes grid - dark theme" },
      "background": "Dark charcoal with soft teal radial glow"
    },
    {
      "headline": "Know What Actually Works",
      "subtitle": "50+ expert dating guides",
      "role": "Show the Learn tab",
      "device": { "model": "iPhone 15 Pro", "screen": "Learn tab with article cards, teal accent" },
      "background": "Dark charcoal with teal gradient glow behind device",
      "references": [{ "image": 1, "usage": "Reproduce this Learn tab layout" }]
    },
    {
      "headline": "Real Convos. Real Wins.",
      "subtitle": "Know what to say",
      "role": "Show conversation coaching",
      "device": { "model": "iPhone 15 Pro", "screen": "Conversation coaching with annotated chat bubbles" },
      "background": "Dark charcoal with subtle teal accent lighting"
    }
  ]
}
```

Panel count must match the platform requirement (iPhone: 3, iPad: 2, Android: 3, Watch: 4). Want more screenshots? Make separate calls.

Wait 60 seconds before the first `jobs.get` poll, then poll every 15 seconds until each returned job is `"complete"` or `"failed"`.

### 7. Present Output

When complete, present the panels in a markdown gallery first, because not all MCP clients render inline HTML. Include each CDN URL and stable screenshot id:

```markdown
| # | Preview | Screenshot ID | URL |
| --- | --- | --- | --- |
| 1 | ![](cdn-url-1) | scr_abc123 | cdn-url-1 |
| 2 | ![](cdn-url-2) | scr_def456 | cdn-url-2 |
| 3 | ![](cdn-url-3) | scr_ghi789 | cdn-url-3 |

[Open in Shots Studio →](https://shots.run/studio?app={appId}&tab=generations)
```

Replace the placeholder URLs, ids, and `{appId}` with actual values from the job result. After the deep link, offer concise next options: revise, generate another set, or localize.
