# Shots Create Flow

Create a new hosted screenshot set from app context, App Store metadata, and the user's positioning goals.

## Steps

### 1. Check Access

Call `get_usage` before doing generation work. If the account is inactive or out of usable screenshots, stop and ask whether the user wants a subscription link. Only call `upgrade_plan` after they confirm.

### 2. Gather Inputs

First check for `.shots/app.json` in the project root. If it contains an `appId`, call `get_app_context` to load the existing app and skip the onboarding questions below.

If the user provides an App Store URL or app id, call `import_app_store_listing` to create or update the durable app record and save the public icon/screenshots to R2. Pass the target locale when known. If the user already has an app record, call `get_app_context`. Also inspect local README/docs/source files if the current workspace is the app repo.

If there is no App Store URL, find an app icon in the local repo before generation when possible. Check Xcode app icon sets, Expo config icon fields, React Native iOS/Android icons, then PWA/public icon fallbacks. Upload it with the bundled helper when the client/runtime can upload local files:

```bash
node {{scripts_path}}/upload-asset.mjs --file ./path/to/icon.png --app-id <appId> --kind icon
```

Use `import_app_asset_from_url` for HTTPS image URLs.

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

Save approved strategy back to the app with `update_app_research`.

### 4. Present Screenshot Plan

Before generation, show a markdown table with one row per requested screenshot. Do not call `generate_screenshots` until the user approves or edits the table.

```markdown
| # | Headline | Subtitle | Image/UI direction | Reference assets | Purpose |
| --- | --- | --- | --- | --- | --- |
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |
```

If the user asks for a specific count, plan exactly that count. If the user asks for more screenshots than the platform allows per job, keep all rows in one table but generate them in batches:

- **iPhone:** up to 3 panels per job
- **iPad:** up to 2 panels per job
- **Android:** up to 3 panels per job
- **Watch:** up to 4 panels per job (2×2 grid)

If there is not enough context to choose the panels confidently, propose 5-10 candidate panel concepts in the same table format and ask the user which ones to generate.

### 5. Build Prompt

Follow [prompting.md](prompting.md). Expand each approved benefit into exact panel instructions: headline, subtitle, visible product UI, background treatment, device framing, and any proof points.

Use up to the platform's max panel count per generation job (iPhone 3, iPad 2, Android 3, Watch 4). For larger requests, make one crop-safe composite prompt per batch.

### 6. Generate

Call `generate_screenshots` with:

- `appId`
- `locale`
- `prompt`
- `platform`
- `panelCount`
- `quality`

For more screenshots than fit in one job, submit multiple jobs. Batch sizes depend on the platform's max panel count:

**iPhone / Android (max 3 per job):**
- 4 screenshots → 3 + 1
- 5 screenshots → 3 + 2
- 6 screenshots → 3 + 3

**iPad (max 2 per job):**
- 3 screenshots → 2 + 1
- 4 screenshots → 2 + 2

**Watch (max 4 per job, 2×2 grid):**
- 8 screenshots → 4 + 4

Wait 60 seconds before the first `get_job` poll, then poll every 15 seconds until the job is `"complete"` or `"failed"`.

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
