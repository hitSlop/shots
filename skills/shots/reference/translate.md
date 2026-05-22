# Shots Localization Flow

Translate an existing screenshot into a target locale while preserving the approved visual system.

## Steps

### 1. Resolve Job And Locale

Ask for the source screenshot id and target locale if missing. If the user provides a job id instead, call `jobs.get`, confirm that it is complete, and choose the relevant generated screenshot id.

### 2. Translate Visible Copy

Translate the screenshot campaign copy and, when the user asks for App Store metadata, save localized title/subtitle/description/keywords with `apps.update_listing` for the target locale:

- keep the same panel roles
- preserve the emotional job of each panel
- adapt idioms where useful
- keep headlines short enough for screenshot readability

Do not redesign the campaign unless the user explicitly asks for locale-specific positioning.

### 3. Build Prompt

Follow [prompting.md](prompting.md). Preserve the visual theme, device framing, panel order, and product promise while swapping in localized headline and subtitle copy.

### 4. Generate And Present

Call `shots` with `screenshots.translate` using the source screenshot id and target `locale`. Wait 60 seconds, then poll the returned job with `jobs.get` every 15 seconds until complete. Present the localized panels in a markdown gallery with screenshot ids:

```markdown
| # | Preview | Screenshot ID | URL |
| --- | --- | --- | --- |
| 1 | ![](cdn-url-1) | scr_abc123 | cdn-url-1 |

[Open in Shots Studio →](https://shots.run/studio?app={appId}&tab=generations)
```

Replace the placeholder URLs, ids, and `{appId}` with actual values from the job result.
