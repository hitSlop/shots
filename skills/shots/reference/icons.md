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
- `referenceMediaIds`
- `referenceScreenshotIds`

### `generate_icon`

- `appId` (required)

Optional:

- `style` — chosen style from moodboard
- `symbol` — main symbol/object for the icon
- `background` — background treatment
- `creative_direction`
- `referenceMediaIds`
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

## Style Families

Choose the best fit for the app. The moodboard can explore several at once; individual icons should commit to one.

- **Glossy 3D / premium dimensional** — polished objects with depth, highlights, and subtle shadows
- **Clay / cute mascot / kawaii** — friendly characters or objects with soft rounded forms and simple faces
- **Flat geometric / minimal** — abstract shapes, clean lines, bold color blocks
- **Glassmorphism** — frosted glass layers, translucency, soft light diffusion
- **Luxury metallic** — gold, silver, or chrome finishes with rich dark backgrounds
- **Bold emblem** — strong shield/badge/crest shapes with confident symbol
- **Gradient abstract** — fluid color gradients with organic or geometric forms

The cute character/mascot style is a dominant trend in the App Store. A friendly character with simple dot eyes, small mouth, rounded silhouette, and playful personality can be very effective for approachable consumer apps.

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

Call `generate_icon` once per chosen concept. Poll per SKILL.md Polling.

#### Example A — Cute mascot/character icon (language learning app)

```json
{
  "appId": "APP_ID",
  "style": "clay/cute mascot",
  "symbol": "A chubby round owl facing forward, bright teal body with a sunny yellow belly patch, two large circular dot eyes with tiny white highlights, small triangular coral beak, tiny stubby wings held slightly out. Expression is warm and encouraging. The owl should look like a soft clay or vinyl toy — tactile and huggable. No outlines, rely on form and shadow for definition. The character fills 70-80% of the canvas.",
  "background": "Smooth radial gradient from bright teal (#4ECDC4) at center to slightly deeper teal (#3DBDB5) at edges, keeping focus on the character",
  "creative_direction": "Create square, upload-ready source artwork for iOS app icon master assets. Full-bleed edge-to-edge artwork, no rounded corners, no rounded-square tile, no icon mockup frame, no outer background, no text. Apple/Xcode will apply platform corner masks later. Kawaii mascot style — the owl should feel like a lovable companion, not a realistic bird. 3D clay-render with soft ambient occlusion and gentle top-down lighting. Personality-driven with minimal detail, maximum charm. Keep the silhouette instantly recognizable even at 29×29 pt."
}
```

#### Example B — Premium dimensional 3D icon (finance app)

```json
{
  "appId": "APP_ID",
  "style": "premium dimensional / glossy 3D",
  "symbol": "A miniature vault door viewed at a slight 3/4 angle, brushed dark steel surface with a circular combination dial in the center. The dial has a softly glowing green (#0F9D58) ring around it. Subtle golden highlight catch on the top edge. Two small bolt heads on either side of the dial.",
  "background": "Deep navy (#1A1A2E) to dark steel blue (#16213E) linear gradient from top-left to bottom-right, with a very faint radial green glow emanating from behind the vault door",
  "creative_direction": "Create square, upload-ready source artwork for iOS app icon master assets. Full-bleed edge-to-edge artwork, no rounded corners, no rounded-square tile, no icon mockup frame, no outer background, no text. Apple/Xcode will apply platform corner masks later. Photorealistic 3D render with studio lighting, metallic materials, and subtle environment reflections. The vault door should feel solid and premium — like a real object you could touch. Avoid flat or cartoonish rendering. The green glow should be subtle, suggesting security and access without being flashy."
}
```

#### Example C — Minimal geometric/flat icon (developer tool)

```json
{
  "appId": "APP_ID",
  "style": "flat geometric / minimal",
  "symbol": "Two angled bracket shapes (like code brackets < >) rendered as bold geometric bars in electric blue (#89B4FA), centered on the canvas. The brackets are slightly rounded at their terminals. A small cursor-line shape between them, same blue, thinner weight.",
  "background": "Solid dark charcoal (#1E1E2E) filling the full canvas edge to edge — no gradients, no texture, pure flat matte",
  "creative_direction": "Create square, upload-ready source artwork for iOS app icon master assets. Full-bleed edge-to-edge artwork, no rounded corners, no rounded-square tile, no icon mockup frame, no outer background, no text. Apple/Xcode will apply platform corner masks later. Flat vector with no gradients on the symbol, single accent color against a dark matte background. The icon reads as 'code' instantly at any size. Electric blue on dark charcoal gives maximum contrast. At 29×29 pt the brackets and cursor should still be crisp and identifiable."
}
```

## Present Results

When the job completes:

1. Call `icons.list` with the `appId`.
2. Present a markdown gallery with preview, asset id, and CDN URL.
3. Ask which candidate should be current.
4. Call `icons.set_current` only after the user chooses.
