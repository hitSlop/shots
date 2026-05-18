# Shots Localization Flow

Translate an existing screenshot job into a target locale while preserving the
approved visual system.

## Steps

### 1. Resolve Job And Locale

Ask for the base job id or target locale if missing. Call `get_job` for the
base job and confirm it is complete.

### 2. Translate Visible Copy

Translate the screenshot campaign copy and, when the user asks for App Store
metadata, save localized title/subtitle/description/keywords with
`update_app_store_listing` for the target locale:

- keep the same panel roles
- preserve the emotional job of each panel
- adapt idioms where useful
- keep headlines short enough for screenshot readability

Do not redesign the campaign unless the user explicitly asks for
locale-specific positioning.

### 3. Build Prompt

Follow [prompting.md](prompting.md). Preserve the visual theme, device framing,
panel order, and product promise while swapping in localized headline and
subtitle copy.

### 4. Generate And Present

Call `generate_screenshots` with the target `locale`, poll with `get_job`, then
present the localized panels side by side in an HTML table with screenshot ids:

```
<table><tr>
<td align="center"><img src="cdn-url-1" width="280"/><br/><sub>scr_abc123</sub></td>
<td align="center"><img src="cdn-url-2" width="280"/><br/><sub>scr_def456</sub></td>
<td align="center"><img src="cdn-url-3" width="280"/><br/><sub>scr_ghi789</sub></td>
</tr></table>

[Open in Shots Studio →](https://shots.run/studio?app={appId}&tab=generations)
```

Replace the placeholder URLs, ids, and `{appId}` with actual values from the
job result.
