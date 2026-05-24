# Shots Create Flow

Create a new hosted screenshot set from app context, App Store metadata, and the user's positioning goals.

## Steps

### 1. Check Access

Check access per SKILL.md Required Setup.

### 2. Gather Inputs

Resolve the app per SKILL.md Project Config and load with `apps.get`. Upload reference images per SKILL.md Reference Images.

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

Before generation, show a markdown table with one row per requested screenshot. Do not call `generate_screenshot` until the user approves or edits the table.

```markdown
| # | Headline | Subtitle | Image/UI direction | Reference assets | Purpose |
| --- | --- | --- | --- | --- | --- |
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |
```

If the user asks for a specific count, plan exactly that count. Call `generate_screenshot` once per approved row. Each call queues one job.

If there is not enough context to choose the panels confidently, propose 5-10 candidate panel concepts in the same table format and ask the user which ones to generate.

### 5. Build Prompt

Follow [prompting.md](prompting.md). Expand each approved benefit into a structured panel: `headline`, `subtitle`, `role`, `layout`, `device`/`devices` with specific screen content, `background`, and optional `breakout_elements` and `references` (mapping reference images by index to usage instructions).

### 6. Generate

Call `generate_screenshot` once per approved row. Each call takes a single `screenshot` object with optional `campaign`, `visual_direction`, and `typography`.

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
  "screenshot": {
    "headline": "37 Scenes. Pick Your Story.",
    "subtitle": "Dinner. Travel. Fitness. More.",
    "role": "Show variety of photo packs",
    "device": { "model": "iPhone 15 Pro", "screen": "Browse scenes grid - dark theme" },
    "background": "Dark charcoal with soft teal radial glow"
  }
}
```

Poll per SKILL.md Polling.

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
