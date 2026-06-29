# App Icon Generation

Use the two-step hosted icon flow:

1. `generate_icon_moodboard` creates a 2048x2048 moodboard with about 20 numbered
   concepts. Cost: 5 generation credits.
2. `generate_icon` creates one 1024x1024 source artwork icon per call. Cost: 3
   generation credits.

Ask before spending credits. Present the moodboard and let the user pick concept
numbers before generating finals.

Icon candidate commands use media-derived access. Use `icons.get`,
`icons.set_current`, and `icons.delete` with the icon `mediaId`; do not pass an
`appId` for those candidate-specific operations.

## Required Direction

Before the moodboard, lock:

- app name and category
- core promise
- audience
- brand colors or mood
- icons or visual references available
- styles to explore

For final icons, ask for or infer:

- chosen moodboard concept number
- symbol or object
- background treatment
- style notes to preserve

## Source Artwork Rules

Prompt for square, full-bleed, upload-ready source artwork:

- 1024x1024 PNG final output
- full color and texture to every edge
- no rounded-corner mask
- no App Store preview tile
- no outer background or presentation card
- no text, labels, watermark, badge, or fake notification
- no black corner voids

Apple and Xcode apply rounded masks later. The generated source should be a full
square asset.

## Moodboard Plan

Use a small planning table before the moodboard:

```markdown
| Direction | Symbol | Style | Why it fits |
| --- | --- | --- | --- |
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
```

Good directions:

- one clear symbol or visual metaphor
- recognizable at small size
- distinct silhouette
- tied to the app's actual value
- fits the app's category norms without disappearing into them

Avoid:

- generic gradients with no symbol
- complex scenes
- tiny UI screenshots inside the icon
- multiple unrelated objects
- unsupported mascots or brand claims

## Style Families

- Character/mascot: education, kids, habit, playful consumer apps
- Dimensional 3D: finance, productivity, creator tools, premium utilities
- Minimal geometric: developer tools, B2B, infrastructure, focused utilities
- Editorial/luxury: fashion, beauty, lifestyle, premium services
- Soft wellness: meditation, family, journaling, health
- Kinetic sport: fitness, events, action apps

## Example Moodboard Prompt

```json
{
  "appId": "APP_ID",
  "creative_direction": "Explore app icon concepts for a calm meal-planning app. The icon should communicate dinner clarity, household warmth, and practical organization without using text. Favor simple shapes, pantry/plate/calendar metaphors, warm olive and golden accents, and strong silhouettes that read at small size.",
  "styles": ["calm warm utility", "minimal geometric", "soft dimensional"]
}
```

## Example Final Icon Prompts

Cute mascot:

```json
{
  "appId": "APP_ID",
  "style": "friendly rounded mascot from moodboard concept 7",
  "symbol": "smiling owl holding a small flashcard",
  "background": "bright warm blue full-bleed background with subtle radial depth",
  "creative_direction": "Upload-ready 1024x1024 square app icon source artwork. No text, no rounded mask, no preview card."
}
```

Premium dimensional:

```json
{
  "appId": "APP_ID",
  "style": "premium dimensional 3D from moodboard concept 3",
  "symbol": "abstract upward bar chart folded into a shield",
  "background": "deep navy full-bleed background with soft teal rim light",
  "creative_direction": "Confident finance icon, simple silhouette, polished material, no text, no outer frame, no rounded-corner preview."
}
```

Minimal geometric:

```json
{
  "appId": "APP_ID",
  "style": "minimal geometric from moodboard concept 12",
  "symbol": "interlocking brackets forming a lightning bolt",
  "background": "full-bleed charcoal square with one electric green accent",
  "creative_direction": "Developer-tool icon, crisp vector-like geometry, high contrast, no text, no border, no preview tile."
}
```
