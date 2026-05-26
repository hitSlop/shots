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
- at least one real app UI screenshot as a reference image (uploaded via `assets.import_url`, the bundled upload helper, or scraped from the App Store via `apps.import`)

An app icon or brand colors alone are not sufficient. Apple requires App Store screenshots to show real app usage, so the generation pipeline needs at least one real screen from the app to reproduce accurate UI in the device frames.

If no app UI screenshots are available:
1. Search the local project for screenshots (e.g. `screenshots/`, `fastlane/screenshots/`, preview images in `assets/` or `docs/`).
2. If found, upload them with the bundled helper using `--kind app_screenshot`.
3. If still none, tell the user: "I need at least one screenshot of your app to use as reference. Please share a screenshot, a URL to one, or point me to a local directory. Apple requires screenshots to show real app usage."
4. Do NOT proceed to step 3 (Build Strategy) or generation until at least one real UI reference is uploaded.

Ask only for missing information that would materially change the panels.

### 3. Build Strategy

Follow [strategy.md](strategy.md). Summarize:

- one-line positioning
- target audience
- visual theme and palette
- 3-5 market words
- first 6-8 benefits
- critical screens available (list which screens have detailed descriptions from the App Experience research — e.g. "Home dashboard, Workout detail, Progress chart, Settings")
- reference asset mapping (which uploaded screenshot or reference image corresponds to which critical screen — e.g. "asset_abc → Home dashboard, asset_def → Workout detail")

Get approval or targeted edits before generation.

Save approved strategy back to the app with `apps.update_research`.

### 4. Present Screenshot Plan

Before generation, show a markdown table with one row per requested screenshot. Do not call `generate_screenshot` until the user approves or edits the table.

Draft the `Headline` column using the Screenshot Title Strategy in [strategy.md](strategy.md): 3-6 word benefit, relief, identity, curiosity, or transformation promises. Do not use feature labels like "AI Dashboard" or "Calendar Sync" unless the user explicitly requests literal feature names. The headline should make sense at thumbnail size without reading the subtitle.

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

Follow [prompting.md](prompting.md). Write a complete JSON prompt for each approved screenshot. Include `task`, `campaign`, `typography`, `visual_direction`, and `screenshot` (with `headline`, `subtitle`, `role`, `layout`, `device`/`devices` with specific screen content, `background`, and optional `breakout_elements`). See the example prompts in prompting.md for the exact format.

### 6. Generate

Call `generate_screenshot` once per approved row. Pass the complete prompt as a JSON string in the `prompt` field. The server appends dimensions, constraints, and reference image metadata.

Select only the reference assets each screenshot actually needs. For example, if a screenshot shows the Day view, pass only the app icon (for palette) and the Day view screenshot reference — not the Week and Month view screenshots. Maximum 4 reference images per call.

```json
{
  "appId": "APP_ID",
  "platform": "iphone",
  "quality": "high",
  "referenceAssetIds": ["ASSET_ID"],
  "prompt": "{\"task\":\"Create one App Store screenshot for \\\"MyApp\\\".\",\"campaign\":{\"goal\":\"Drive downloads from App Store search\",\"audience\":\"Men 22-38 wanting better dating photos\",\"core_promise\":\"Dating-specific AI photos that look real\",\"differentiator\":\"37 themed photo scenes, not generic AI headshots\"},\"visual_direction\":{\"style_family\":\"clean-premium\",\"mood\":\"dark, confident, aspirational\",\"palette\":{\"primary\":\"#0F1416\",\"accent\":\"#119DA4\",\"text\":\"#FFFFFF\"}},\"typography\":{\"headline_style\":\"Large, bold, white sans-serif, high-contrast.\",\"subtitle_style\":\"Smaller, regular weight, light gray.\",\"text_accuracy\":\"Render all quoted text verbatim. >95% accuracy.\"},\"screenshot\":{\"headline\":\"37 Scenes. Pick Your Story.\",\"subtitle\":\"Dinner. Travel. Fitness. More.\",\"role\":\"Show variety of photo packs\",\"device\":{\"model\":\"iPhone 15 Pro\",\"screen\":\"Browse scenes grid - dark theme\"},\"background\":\"Dark charcoal with soft teal radial glow\"}}"
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

Replace the placeholder URLs, ids, and `{appId}` with actual values from the job result.

After all jobs complete, build a review URL with all screenshot IDs:
`https://shots.run/review?ids={id1},{id2},{id3}`

Share this URL — it opens a no-auth review page showing all generated screenshots. After sharing, use your embedded browser plugin to navigate to the review URL so the user can see the results immediately inside the app.

After the deep link, offer concise next options: revise, generate another set, or localize.
