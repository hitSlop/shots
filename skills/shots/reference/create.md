# Screenshot Creation

Create one approved screenshot prompt per panel, then call `generate_screenshot`
once per panel. The hosted MCP instructions handle setup, billing, polling, and
review URLs.

## Inputs To Lock

- Target app identity: app name, what it does, target platform, and App Store URL
  if published.
- Audience and positioning: who should install, what pain they feel, and what
  outcome the app promises.
- Real UI references: at least one uploaded app screenshot, reference image
  showing actual UI, or imported App Store screenshot.
- Optional inspiration: public gallery screenshots from another app, used only
  for style, composition, color, typography, and pacing.

Do not generate from only an icon, brand colors, or written app description.
Apple requires screenshots to show real app usage, and invented UI risks a weak
or unusable result.

## Pre-Generation Checks

- Load the app with `apps.get`.
- If no real UI reference exists, search the local repo for screenshots,
  fastlane screenshots, preview images, docs images, or product mockups. Upload
  good candidates with the helper using `--kind app_screenshot`.
- Before planning new English screenshots, check `screenshots.listing` for
  selected/promoted `en-US` store screenshots. If none exist, check recent
  `screenshots.list` results. Use the best existing campaign screenshot as a
  style reference when available.
- If the user gives another App Store URL as inspiration, use
  `gallery.ensure_app` and `gallery.get_app`; do not import it as the user's app
  unless they explicitly say it is theirs.

## Strategy Snapshot

Before writing panels, summarize and save:

- one-line positioning
- target audience
- visual direction and palette
- 3-5 market-native words
- available critical screens
- mapping of reference assets to screens
- selected/promoted or recent English campaign screenshots used for style
- public gallery inspiration ids, if used

Store durable findings with `apps.update_research` so Studio and future agents
share the same context.

## Screenshot Plan

Show a table and wait for approval before generation.

```markdown
| # | Headline | Subtitle | Image/UI direction | Reference assets | Purpose |
| --- | --- | --- | --- | --- | --- |
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |
```

Planning rules:

- Honor an explicit screenshot count exactly.
- During first-time setup only, suggest 3 screenshots when the user wants
  screenshots but gives no count.
- Headline first: 3-6 word benefit, relief, identity, curiosity, or
  transformation promise.
- Avoid feature labels like "AI Dashboard" unless the user asks for literal
  feature naming.
- Each row needs a specific UI moment, not generic "show the app" direction.
- The `Reference assets` column must name the real UI reference and any style or
  public inspiration reference used by that row.

If context is thin, propose 5-10 candidate panel concepts and ask which ones to
generate.

## Generate

For each approved row:

- Build a complete prompt using [prompting.md](prompting.md).
- Pass only the references that row actually needs, up to the server's max.
- Include `galleryInspirationScreenshotId` only when that row uses public
  gallery inspiration.
- Use existing English campaign screenshots for style continuity, not old copy.

Example:

```json
{
  "appId": "APP_ID",
  "platform": "iphone",
  "quality": "high",
  "referenceMediaIds": ["MEDIA_ID_REAL_UI", "MEDIA_ID_STYLE"],
  "galleryInspirationScreenshotId": "GALLERY_SCREENSHOT_ID",
  "prompt": "{\"task\":\"Create one App Store screenshot for \\\"MyApp\\\".\",\"campaign\":{\"goal\":\"Drive installs from App Store search\",\"audience\":\"Busy parents planning meals\",\"core_promise\":\"Dinner decisions feel automatic\",\"differentiator\":\"Meal plans adapt to pantry items and schedule changes\"},\"typography\":{\"headline_style\":\"Large bold white sans-serif, high contrast, thumbnail-readable.\",\"subtitle_style\":\"Smaller warm white regular weight.\",\"text_accuracy\":\"Render all quoted text verbatim.\"},\"visual_direction\":{\"style_family\":\"calm-warm\",\"mood\":\"organized, practical, reassuring\",\"palette\":{\"primary\":\"#243B35\",\"accent\":\"#FFB84D\",\"text\":\"#FFFFFF\"}},\"screenshot\":{\"headline\":\"Know Dinner by 5\",\"subtitle\":\"Plans that adapt when life changes\",\"role\":\"hero benefit\",\"layout\":\"headline top, iPhone centered below, warm kitchen-toned background\",\"device\":{\"model\":\"iPhone 15 Pro\",\"screen\":\"Weekly meal plan screen with Monday selected, three recipe cards, pantry match badges, and a yellow Swap Dinner button\"},\"background\":\"soft olive-to-cream gradient with subtle grocery-list texture\",\"negative_constraints\":\"No App Store badges, no fake chrome, no clipped text, no fictional UI unrelated to references\"}}"
}
```

After jobs complete, present screenshot ids, CDN URLs, and the review URL the
server instructions describe.
