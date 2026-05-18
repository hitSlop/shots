# Shots Create Flow

Create a new hosted screenshot set from app context, App Store metadata, and
the user's positioning goals.

## Steps

### 1. Check Access

Call `get_usage` before doing generation work. If the account is inactive or
out of usable screenshots, stop and ask whether the user wants a subscription link.
Only call `upgrade_plan` after they confirm.

### 2. Gather Inputs

First check for `.shots/app.json` in the project root. If it contains an
`appId`, call `get_app_context` to load the existing app and skip the
onboarding questions below.

If the user provides an App Store URL or app id, call
`import_app_store_listing` to create or update the durable app record and save
the public icon/screenshots to R2. Pass the target locale when known. If the
user already has an app record, call `get_app_context`. Also inspect local
README/docs/source files if the current workspace is the app repo.

If there is no App Store URL, find an app icon in the local repo before
generation when possible. Check Xcode app icon sets, Expo config icon fields,
React Native iOS/Android icons, then PWA/public icon fallbacks. Upload it with
`upload_app_asset` as `kind: "icon"`.

Minimum signal:

- `appName` or a clearly identified product
- app description or category
- at least one visual cue: App Store screenshots, app icon, brand colors, or
  existing product UI in the repo

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

### 4. Build Prompt

Follow [prompting.md](prompting.md). Expand each approved benefit into exact
panel instructions: headline, subtitle, visible product UI, background
treatment, device framing, and any proof points.

Use up to three panels per generation job.

### 5. Generate

Call `generate_screenshots` with:

- `appId`
- `locale`
- `prompt`
- `platform`
- `panelCount`
- `quality`

Wait 60 seconds before the first `get_job` poll, then poll every 15 seconds
until the job is `"complete"` or `"failed"`.

### 6. Present Output

When complete, present the panels side by side in an HTML table so they render
as an inline gallery. Place each panel's CDN URL in an `<img>` tag and its
stable screenshot id beneath it:

```
<table><tr>
<td align="center"><img src="cdn-url-1" width="280"/><br/><sub>scr_abc123</sub></td>
<td align="center"><img src="cdn-url-2" width="280"/><br/><sub>scr_def456</sub></td>
<td align="center"><img src="cdn-url-3" width="280"/><br/><sub>scr_ghi789</sub></td>
</tr></table>

[Open in Shots Studio →](https://shots.run/studio?app={appId}&tab=generations)
```

Replace the placeholder URLs, ids, and `{appId}` with actual values from the
job result. After the deep link, offer concise next options: revise, generate
another set, or localize.
