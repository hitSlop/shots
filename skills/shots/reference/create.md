# Shots Create Flow

Create a new hosted screenshot set from app context, App Store metadata, and the user's positioning goals.

## Steps

### 1. Check Access

Check access per SKILL.md Required Setup.

### 2. Gather Inputs

Resolve the app per SKILL.md Project Config and load with `apps.get`. Upload reference images per SKILL.md Reference Images.

If the user provides another App Store URL as style inspiration, do not import it as the user's app. Follow SKILL.md Public Gallery Inspiration: call `gallery.ensure_app`, then `gallery.get_app`, and keep the returned gallery screenshot ids for the plan.

Minimum signal:

- `appName` or a clearly identified product
- app description or category
- at least one real app UI screenshot as a reference image (uploaded via `media.import_url`, the bundled upload helper, or scraped from the App Store via `apps.import`)
- optional public gallery inspiration from `gallery.get_app`

An app icon or brand colors alone are not sufficient. Apple requires App Store screenshots to show real app usage, so the generation pipeline needs at least one real screen from the app to reproduce accurate UI in the device frames.

If no app UI screenshots are available:
1. Search the local project for screenshots (e.g. `screenshots/`, `fastlane/screenshots/`, preview images in `assets/` or `docs/`).
2. If found, upload them with the bundled helper using `--kind app_screenshot`.
3. If still none, tell the user: "I need at least one screenshot of your app to use as reference. Please share a screenshot, a URL to one, or point me to a local directory. Apple requires screenshots to show real app usage."
4. Do NOT proceed to step 3 (Build Strategy) or generation until at least one real UI reference is uploaded.

Before planning new screenshots, check existing English campaign screenshots:

1. Call `screenshots.listing` with `appId`, the target `platform`, and `locale: "en-US"` to load selected/promoted store screenshots.
2. If no selected/promoted English screenshots exist, call `screenshots.list` with `appId`, `locale: "en-US"`, and a small limit such as 12 to load recent generated screenshots.
3. Prefer selected/promoted screenshots as style references. Use recent generated screenshots only as fallback.
4. If any existing screenshot is available, include at least one relevant screenshot in the later `referenceMediaIds` for each new generation call. Keep the real app UI reference too; existing generated screenshots guide campaign style, not product accuracy.

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
- English campaign screenshot mapping (which selected/promoted or recent generated screenshot will be used for style continuity, including its saved prompt when available)
- public inspiration mapping when used (which `galleryScreenshotId` maps to which planned panel)

Get approval or targeted edits before generation.

Save approved strategy back to the app with `apps.update_research`.

### 4. Present Screenshot Plan

Before generation, show a markdown table with one row per requested screenshot. Do not call `generate_screenshot` until the user approves or edits the table.

During first-time app setup only, if the user wants screenshots and does not specify a count, suggest starting with 3 screenshots. Do not re-suggest 3 for later generation requests. If the user asks for a specific count, plan exactly that count.

Draft the `Headline` column using the Screenshot Title Strategy in [strategy.md](strategy.md): 3-6 word benefit, relief, identity, curiosity, or transformation promises. Do not use feature labels like "AI Dashboard" or "Calendar Sync" unless the user explicitly requests literal feature names. The headline should make sense at thumbnail size without reading the subtitle.

```markdown
| # | Headline | Subtitle | Image/UI direction | Reference assets | Purpose |
| --- | --- | --- | --- | --- | --- |
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |
```

The `Reference assets` column must name the real app UI screenshot and, when one exists, the selected/promoted English screenshot or recent English generated screenshot being used for campaign style. Call `generate_screenshot` once per approved row. Each call queues one job.

If there is not enough context to choose the panels confidently, propose 5-10 candidate panel concepts in the same table format and ask the user which ones to generate.

### 5. Build Prompt

Follow [prompting.md](prompting.md). Write a complete JSON prompt for each approved screenshot. Include `task`, `campaign`, `typography`, `visual_direction`, and `screenshot` (with `headline`, `subtitle`, `role`, `layout`, `device`/`devices` with specific screen content, `background`, and optional `breakout_elements`). See the example prompts in prompting.md for the exact format.

### 6. Generate

Call `generate_screenshot` once per approved row. Pass the complete prompt as a JSON string in the `prompt` field. The server appends dimensions, constraints, and reference image metadata.

Select only the reference assets each screenshot actually needs. For example, if a screenshot shows the Day view, pass the Day view real app UI reference plus one existing English campaign screenshot for style consistency; do not pass every Week and Month view screenshot. Maximum 4 reference images per call.

When an existing selected/promoted or saved screenshot includes a `prompt`, summarize the relevant style instructions from that prompt into the new prompt's `visual_direction` and reference usage notes: palette, typography, layout rhythm, background language, device treatment, and overall campaign vibe. Do not copy old headline/subtitle copy unless the user asks to reuse it.

When the row uses public gallery inspiration, also pass the most relevant `galleryInspirationScreenshotId`. Use that gallery screenshot for style, composition, typography, tone, and campaign pacing only. Do not copy the inspiration app UI or branding.

```json
{
  "appId": "APP_ID",
  "platform": "iphone",
  "quality": "high",
  "referenceMediaIds": ["MEDIA_ID"],
  "prompt": "{\"task\":\"Create one App Store screenshot for \\\"MyApp\\\".\",\"campaign\":{\"goal\":\"Drive downloads from App Store search\",\"audience\":\"Men 22-38 wanting better dating photos\",\"core_promise\":\"Dating-specific AI photos that look real\",\"differentiator\":\"37 themed photo scenes, not generic AI headshots\"},\"visual_direction\":{\"style_family\":\"clean-premium\",\"mood\":\"dark, confident, aspirational\",\"palette\":{\"primary\":\"#0F1416\",\"accent\":\"#119DA4\",\"text\":\"#FFFFFF\"}},\"typography\":{\"headline_style\":\"Large, bold, white sans-serif, high-contrast.\",\"subtitle_style\":\"Smaller, regular weight, light gray.\",\"text_accuracy\":\"Render all quoted text verbatim. >95% accuracy.\"},\"screenshot\":{\"headline\":\"37 Scenes. Pick Your Story.\",\"subtitle\":\"Dinner. Travel. Fitness. More.\",\"role\":\"Show variety of photo packs\",\"device\":{\"model\":\"iPhone 15 Pro\",\"screen\":\"Browse scenes grid - dark theme\"},\"background\":\"Dark charcoal with soft teal radial glow\"}}"
}
```

Poll per SKILL.md Polling.

### 7. Present Output

When complete, present the panels in a markdown gallery first, because not all MCP clients render inline HTML. Include each CDN URL and stable screenshot id:

```markdown
| # | Preview | Screenshot ID | URL |
| --- | --- | --- | --- |
| 1 | ![](image-url-1) | scr_abc123 | image-url-1 |
| 2 | ![](image-url-2) | scr_def456 | image-url-2 |
| 3 | ![](image-url-3) | scr_ghi789 | image-url-3 |

[Open in Shots Studio →](https://shots.run/studio?app={appId}&tab=generations)
```

Replace the placeholder URLs, ids, and `{appId}` with actual values from the job result.

After all jobs complete, build a review URL with all screenshot IDs:
`https://shots.run/review?ids={id1},{id2},{id3}`

Share this URL — it opens a no-auth review page showing all generated screenshots. After sharing, use your embedded browser plugin to navigate to the review URL so the user can see the results immediately inside the app.

After the deep link, offer concise next options: revise, generate another set, or localize.
