# App Icon Generation

Use the two-step icon flow: `generate_icon_moodboard` to brainstorm, then `generate_icon` to produce finals. These are not generic image tools and are not rounded App Store preview generators.

## What The Tools Do

- **`generate_icon_moodboard`** creates a 2048×2048 moodboard image with ~20 numbered icon concepts (5 credits). Present the moodboard to the user and ask them to pick favorites by number.
- **`generate_icon`** generates one individual 1024×1024 PNG icon per call (3 credits each). Call it once per chosen concept from the moodboard.

Both steps cost design credits, so plan first and get approval.

## Required Inputs

### `generate_icon_moodboard`

- `appId` (required)

Optional:

- `creative_direction`
- `styles` — style directions to explore
- `referenceAssetIds`
- `referenceScreenshotIds`

### `generate_icon`

- `appId` (required)

Optional:

- `style` — chosen style from moodboard
- `symbol` — main symbol/object for the icon
- `background` — background treatment
- `creative_direction`
- `referenceAssetIds`
- `referenceScreenshotIds`

## Critical Source Artwork Rules

Prompt for square source artwork that can be uploaded directly as the 1024x1024 master icon asset. The generated image should fill the entire square tile edge to edge.

Required:

- square 1024x1024 source artwork
- full-bleed color, lighting, texture, and composition to every edge
- one clear focal symbol only
- readable silhouette at small sizes
- strong foreground/background separation
- vibrant, high-contrast palette
- polished finish with subtle depth, highlights, and shadows

Banned:

- rounded corners
- rounded-square masks
- App Store preview tiles
- icon mockup frames
- cards or framed containers
- outer backgrounds around the icon
- black corner voids
- visible tile edges
- text, letters, numbers, labels, badges, logos, watermarks, or App Store chrome

Apple, Xcode, App Store, iOS, iPadOS, and watchOS apply platform corner masks later. Do not ask the model to create those masks.

## Planning Table

Before calling `generate_icon_moodboard`, present a direction table and wait for approval. This guides what the moodboard explores:

```markdown
| # | Style | Main symbol | Background | Notes |
| --- | --- | --- | --- | --- |
| 1 | Glossy hero object |  |  | High-conversion premium candidate |
| 2 | Playful clay 3D |  |  | Softer, friendlier candidate |
| 3 | Glassmorphism glyph |  |  | Light, luminous, modern candidate |
| 4 | Bold geometric emblem |  |  | More graphic and logo-like |
| 5 | Cute mascot-like |  |  | Expressive but still simple |
| 6 | Luxury metallic/plastic |  |  | Mature premium candidate |
```

Keep the directions meaningfully different. The moodboard will explore ~20 variations. After the user reviews the moodboard and picks favorites by number, call `generate_icon` for each chosen concept.

## Prompting Pattern

Use this source-artwork language in `creative_direction`:

```text
Create square, upload-ready source artwork for iOS app icon master assets. Full-bleed edge-to-edge artwork, no rounded corners, no rounded-square tile, no icon mockup frame, no outer background, no text. Apple/Xcode will apply platform corner masks later.
```

Then add the app-specific symbol and style direction. Prefer "source artwork", "full-bleed square", and "edge-to-edge" over "iOS app icon", which can make the model draw rounded preview tiles.

## Generate

### Step 1: Moodboard

```json
{
  "appId": "APP_ID",
  "creative_direction": "Create square, upload-ready source artwork for iOS app icon master assets. Full-bleed edge-to-edge artwork, no rounded corners, no rounded-square tile, no icon mockup frame, no outer background, no text. Apple/Xcode will apply platform corner masks later. Premium, polished, high-contrast, readable at small App Store sizes. Explore glossy, clay 3D, glassmorphism, geometric, mascot, and metallic styles around a camera lens with sparkle.",
  "styles": ["glossy hero object", "playful clay 3D", "glassmorphism glyph", "bold geometric emblem", "cute mascot-like", "luxury metallic/plastic"]
}
```

Poll per SKILL.md Polling. When complete, present the moodboard and ask the user to pick favorites by number (e.g. "3, 7, 14").

### Step 2: Individual Icon

```json
{
  "appId": "APP_ID",
  "style": "glossy hero object",
  "symbol": "large black camera lens with a white sparkle",
  "background": "full-bleed purple, magenta, and cyan light field",
  "creative_direction": "Create square, upload-ready source artwork for iOS app icon master assets. Full-bleed edge-to-edge artwork, no rounded corners, no rounded-square tile, no icon mockup frame, no outer background, no text. Apple/Xcode will apply platform corner masks later. Premium, dramatic, one dominant lens object."
}
```

Call `generate_icon` once per chosen concept. Poll per SKILL.md Polling.

## Present Results

When the job completes:

1. Call `icons.list` with the `appId`.
2. Present a markdown gallery with preview, asset id, and CDN URL.
3. Ask which candidate should be current.
4. Call `icons.set_current` only after the user chooses.
