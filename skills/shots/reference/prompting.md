# Screenshot Prompting

Send a complete prompt to `generate_screenshot` in the `prompt` field. JSON is
preferred because it keeps campaign logic, typography, references, and device UI
separate. Natural language is accepted when the client or user needs it.

Each `generate_screenshot` call creates one App Store screenshot. The server
adds dimensions, platform constraints, and reference metadata.

## Contract

Every prompt should answer:

- What app and campaign is this for?
- Who is the target user?
- What single promise should this panel make?
- Which real app screen should appear inside the device?
- Which references should influence UI accuracy, style, brand, or inspiration?
- What must be avoided?

Use visual facts over praise words. Avoid vague terms like `stunning`,
`beautiful`, `masterpiece`, `innovative`, and `seamless`.

## Recommended JSON Shape

```json
{
  "task": "Create one App Store screenshot for \"AppName\".",
  "campaign": {
    "goal": "",
    "category": "",
    "audience": "",
    "core_promise": "",
    "differentiator": ""
  },
  "typography": {
    "headline_style": "",
    "subtitle_style": "",
    "text_accuracy": "Render all quoted text verbatim."
  },
  "visual_direction": {
    "style_family": "",
    "mood": "",
    "palette": {},
    "motifs": [],
    "reference_usage": []
  },
  "screenshot": {
    "headline": "",
    "subtitle": "",
    "role": "",
    "layout": "",
    "device": {
      "model": "",
      "frame": "",
      "screen": ""
    },
    "background": "",
    "breakout_elements": "",
    "negative_constraints": ""
  }
}
```

## Text Rendering

- Put exact visible copy in quotes.
- Specify font style, weight, color, and hierarchy.
- Spell unusual brand names when needed.
- Keep headlines short enough to survive thumbnail browsing.
- Make the headline 2-3x larger than supporting text.
- Keep critical text in the top two-thirds of the panel.

## Device Content

Use the App Experience research or real reference images to describe the screen
inside the device. Be specific about layout, data, and UI states.

Good:

- `Home dashboard with nav bar, search field, three filter chips, four habit
  cards with circular progress rings, streak counters, and a bottom tab bar`
- `Photo ranking screen with three profile photo cards, a mint 9.7 score badge,
  rows for Lighting, Expression, and Composition, and a black ANALYZE button`

Bad:

- `Shows the app`
- `A clean dashboard with some cards`

If no real UI reference or detailed screen description exists, stop and ask for
one before generating.

## Reference Usage

Call out exactly what each reference should do:

```text
Reference image usage:
- Reference 1: real app UI layout, visible controls, and product accuracy.
- Reference 2: existing English campaign style, palette, typography, device treatment, and background rhythm.
- Reference 3: app icon color and brand symbol language.
- Gallery inspiration: style and composition only; do not copy its UI or branding.
```

Never say only "use references." The generation model needs to know which image
controls product accuracy and which image controls style.

## Visual Defaults

- One promise per panel.
- Device should not stretch to fill the canvas.
- Use real UI as the proof, not generic decorative UI.
- Backgrounds should support the promise without overpowering the device.
- Breakout elements must relate to actual UI or the app's value.
- Do not request fake App Store chrome, badges, gutters, borders, dividers, or
  rounded screenshot frames.

## Platform Notes

- iPhone: use iPhone 15 Pro or iPhone 16 Pro frames unless the user asks
  otherwise. Device usually works best at roughly 55-65% canvas width.
- iPad: wider layouts need larger typography and often work better in landscape.
- Android: use Pixel or Samsung framing; do not mention iOS-only UI like Dynamic
  Island.
- Watch: keep copy extremely short and prioritize big, high-contrast iconography.

## Example 1: Warm Consumer Utility

```json
{
  "task": "Create one App Store screenshot for \"PantryPlan\".",
  "campaign": {
    "goal": "Drive installs from App Store search",
    "category": "Food & Drink / Meal Planning",
    "audience": "Busy parents who decide dinner late in the day",
    "core_promise": "Dinner planning feels calm and automatic",
    "differentiator": "Plans adapt to pantry items, family preferences, and schedule changes"
  },
  "typography": {
    "headline_style": "Large, bold, warm white sans-serif, high-contrast, thumbnail-readable.",
    "subtitle_style": "Smaller regular-weight cream text with generous line height.",
    "text_accuracy": "Render all quoted text verbatim."
  },
  "visual_direction": {
    "style_family": "calm-warm",
    "mood": "organized, practical, reassuring",
    "palette": {
      "primary": "#243B35",
      "secondary": "#F6EFE4",
      "accent": "#FFB84D",
      "text": "#FFFFFF"
    },
    "motifs": ["soft grocery-list texture", "warm kitchen light", "rounded recipe cards"],
    "reference_usage": [
      "Use the real app screenshot for the meal plan UI layout and card hierarchy.",
      "Use the app icon only for palette and brand warmth."
    ]
  },
  "screenshot": {
    "headline": "Know Dinner by 5",
    "subtitle": "Plans that adapt when life changes",
    "role": "Hero panel proving the main relief",
    "layout": "Headline and subtitle at top, centered iPhone below with slight float, small pantry card breakout on the right.",
    "device": {
      "model": "iPhone 15 Pro",
      "frame": "natural titanium, thin bezels",
      "screen": "Weekly meal plan screen with Monday selected, three recipe cards, pantry match badges, and a yellow Swap Dinner button"
    },
    "background": "Soft olive-to-cream gradient with subtle grocery-list texture and warm glow behind device",
    "breakout_elements": "One rounded pantry card reading \"Use tonight\" with tomato, pasta, and basil icons, fully inside crop",
    "negative_constraints": "No App Store badges, no fake chrome, no clipped text, no fictional UI unrelated to references"
  }
}
```

## Example 2: Dark Premium Photo App

```json
{
  "task": "Create one App Store screenshot for \"Pull\".",
  "campaign": {
    "goal": "Showcase AI photo improvement for dating profiles",
    "category": "Photo & Video / Dating",
    "audience": "Young adults who want better profile photos without looking fake",
    "core_promise": "More confident photos that still look like you",
    "differentiator": "Subtle AI ranking and enhancement tuned for dating apps"
  },
  "typography": {
    "headline_style": "Large bold white sans-serif with slight shadow for contrast.",
    "subtitle_style": "Smaller light gray text, clean and direct.",
    "text_accuracy": "Render all quoted text verbatim."
  },
  "visual_direction": {
    "style_family": "dark-premium",
    "mood": "confident, modern, aspirational, not cold",
    "palette": {
      "primary": "#0A0A0F",
      "secondary": "#1A1A2E",
      "accent": "#FF6B6B",
      "text": "#FFFFFF",
      "subtitle": "#A0A0A0"
    },
    "motifs": ["subtle warm glow", "clean geometric framing", "small like notification chips"],
    "reference_usage": [
      "Use the real app screenshot for ranking UI, score rows, and button style.",
      "Use the existing campaign screenshot for dark palette, typography scale, and device treatment."
    ]
  },
  "screenshot": {
    "headline": "Photos That Get Replies",
    "subtitle": "AI feedback that looks natural",
    "role": "Transformation proof panel",
    "layout": "Headline at top, centered iPhone below, small score badge breakout near the upper-right device edge.",
    "device": {
      "model": "iPhone 16 Pro",
      "frame": "black titanium",
      "screen": "Photo ranking screen with three profile photo cards, a 9.7 score badge, rows for Lighting, Expression, and Composition, and a black Analyze button"
    },
    "background": "Near-black to dark navy gradient with a warm coral glow behind the device",
    "breakout_elements": "One floating coral badge reading \"9.7 Match Ready\" and two subtle heart notification chips, all inside crop",
    "negative_constraints": "No App Store badges, no fake app UI, no exaggerated beauty claims, no clipped text"
  }
}
```

## Revision Block

For revisions, append a short block to preserve what already works:

```text
REVISION INSTRUCTIONS

Change:
- {exact requested change}

Preserve:
- positioning, panel role, crop safety
- theme, palette, and typography unless explicitly changed
- unchanged device UI and approved copy

Constraints:
- keep everything else stable
```
