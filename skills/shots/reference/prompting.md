# Shots Prompting Contract

Turn the saved `strategyBrief` and chosen `benefits` into one crop-safe prompt per approved screenshot.

## Objective

Create App Store screenshots that read clearly after server-side compositing and cropping. Optimize for small-screen clarity and install intent first.

`generate_screenshots` accepts a `screenshots` array. Each entry is one final screenshot prompt. The server groups compatible entries into composite generation jobs and crops them into individual screenshots. Do not manually batch prompts or ask for multiple framed screenshots inside one prompt. Do not add rounded panel corners, panel borders, gutters, dividers, or mock App Store chrome. The crop itself supplies the final screenshot boundary.

## Rules

- use visual facts, not praise words
- use market-native wording from `strategyBrief.voice`
- do not copy competitor claims verbatim
- keep copy short and verbatim
- device screens must show specific UI
- decorative elements must support the promise, not distract from it
- optimize for browse-feed stopping power before full-size polish
- treat panel 1 like it may be the only panel the user really notices
- existing screenshots are visual reference, not copy reference
- create exactly one prompt per approved screenshot
- preserve crop safety: every panel must stand alone after cropping

Avoid words like:

- `stunning`
- `beautiful`
- `masterpiece`
- `innovative`
- `seamless`

## Text Rendering Guidelines (GPT Image 2)

GPT Image 2 achieves >95% text accuracy when following these rules:

### Quotation Marks for Exact Copy

Place exact copy in double quotes:
- ✅ "Meditate for 5 minutes daily"
- ✅ "Track Your Habits"
- ❌ Write text about meditation and tracking

### Font Specification

Always specify:
- Font style (sans-serif, modern, geometric, rounded)
- Weight (bold, semibold, regular, light)
- Color (hex or descriptive with brand context)
- Size relationship (large headline, small subtext)

Example: "Bold sans-serif headline in #FF5722, large and high-contrast"

### Verbatim Rendering for Critical Text

For brand names or specific phrases, request verbatim:
"The app name 'HabitFlow' should appear verbatim in the top-left corner"

### Spell Out Tricky Names

For unusual spellings or made-up words:
"App name: M-I-N-D-S-P-A-C-E (all caps, spaced)"

## Visual Hierarchy Rules

### Size Contrast

- Headline: 2-3× larger than body text
- Primary CTA or benefit: Visually dominant in top 2/3 of frame
- Supporting details: Secondary visual weight

### Color Emphasis

- Accent color reserved for 1-2 key elements per panel
- High contrast between text and background (WCAG AA minimum)
- Brand primary color used strategically, not everywhere

### Positioning Strategy

- Critical info in top 2/3 (thumb-scrollable zone on product page)
- Visual entry point in top-left or center
- Device mockup should not obscure headline

### White Space

- Breathing room around focal points (minimum 40px padding)
- Avoid cramming - each panel has ONE primary message
- Empty space guides the eye to important elements

### Dynamic Visual Language

- Use color, character, and warmth (avoid sterile AI aesthetics)
- Include human elements: hands holding device, lifestyle context
- Personality > polish when in doubt

### Visual Style

Choose the style that best matches the app's brand, audience, and category. The goal is screenshots that feel native to the product and convert well for the target user base — not a one-size-fits-all look.

**Common styles:**

| Style | When to use | Key traits |
| --- | --- | --- |
| **3D / Pop-out depth** | Bold consumer apps, games, social, lifestyle | Layered depth (background → device → foreground breakout elements), gradient orbs, floating UI cards with drop shadows, slight rotation on breakout pieces |
| **Clean / Minimal** | Productivity, finance, developer tools, B2B | Flat or subtle gradient backgrounds, device centered with generous white space, no breakout elements, focus on UI clarity |
| **Lifestyle / Editorial** | Health, fitness, food, travel, fashion | Photography-led backgrounds, warm lighting, human elements (hands, context), device integrated into a real-world scene |
| **Gradient / Abstract** | Music, creative tools, social media | Rich color gradients, geometric shapes, vibrant palette, device as focal point within an expressive color field |
| **Dark / Premium** | Pro tools, media, photo/video editors | Deep dark backgrounds, subtle highlights and glows, high-contrast text, minimal decoration, luxury feel |
| **Playful / Illustrated** | Kids, education, casual games, pets | Custom illustrations, mascots, hand-drawn textures, rounded shapes, bright saturated colors |

Pick the style that a real designer would use for this app. When in doubt, study competitors in the same App Store category and match or improve on the prevailing visual language.

**If using breakout / pop-out elements:**
- Max 1-2 per panel — more feels cluttered
- Each gets a drop shadow (offset ~6 px, blur ~16 px, 30-40% opacity)
- Slight rotation (3-8°) adds life
- Elements must relate to actual app UI (a real card, stat, icon), not generic decoration
- Text-bearing breakout elements must sit fully inside the crop zone — never clipped at panel edges

**Background guidelines (all styles):**
- Avoid flat solid colors with nothing else — even minimal styles benefit from a subtle gradient or texture
- Background should support the focal point, not compete with it

## Panoramic Background (Optional)

For cohesive multi-panel narratives, repeat a shared background direction across screenshot prompts:

"Use a continuous campaign background language: [color1] to [color2] gradient, [geometric element], and consistent lighting across all screenshots. This screenshot should feel like panel {n} of the same campaign while standing alone."

Benefits:
- Creates visual tension that compels next-screenshot viewing
- Reinforces narrative flow across panels
- Professional, high-production feel

Use when: Brand has strong visual identity, story needs strong continuity
Avoid when: Each panel needs fully independent context

## Prompt Structure (5-Element Framework)

### 1. Style/Medium

"A vibrant, modern app store marketing screenshot in a [design style]"

### 2. Subject Description

"Featuring an iPhone 15 Pro displaying [specific UI screen] with the headline '[exact text]' in [font details]"

### 3. Composition/Framing

"The device is positioned in the [left/center/right] third, angled slightly, with [background element] filling the negative space"

### 4. Lighting & Color

"Soft gradient background from [color] to [color], with accent lighting in [brand color] creating depth"

### 5. Negative Constraints

"No badges, no app store buttons, no fake UI chrome, no generic stock photo people, no Lorem Ipsum text, no text clipped at panel crop boundaries"

### Example Full Prompt

"A vibrant, modern app store marketing screenshot featuring an iPhone 15 Pro displaying the Meditation Timer screen with the headline 'Find Your Calm in 5 Minutes' in bold Helvetica Neue, white text with slight shadow for contrast. The device is positioned in the left third, angled 15° clockwise, with a serene gradient background from soft lavender (#E6E6FA) to pale blue (#B0C4DE). Floating abstract lotus petal shapes in the background create depth without distraction. The app screen shows a circular timer interface with purple accent color (#7B68EE), surrounded by subtle glow. Clean, professional, and inviting aesthetic. No badges, no borders, no fake app store elements."

## Structured JSON Prompt

Prompts are now structured JSON objects built by the server from agent input via `composeGenerationPrompt()`. The agent sends structured fields through `generate_screenshots`; the server injects dimensions, format, app context, reference image metadata, safe-zone guidance, and constraints.

### Output Shape

The composed prompt is a JSON object with these top-level keys:

```json
{
  "task": "Create one 3360x2432 App Store marketing composite for \"AppName\".",
  "format": {
    "canvas": "3360x2432",
    "layout": "3 equal-width vertical panels, left-to-right",
    "panel_crop": "Each panel crops cleanly to 1120x2432.",
    "seams": "No visible seams, dividers, borders, or gutters."
  },
  "campaign": { "goal": "...", "audience": "...", "app_name": "...", "..." : "..." },
  "visual_direction": { "style_family": "...", "mood": "...", "palette": {}, "motifs": [] },
  "typography": { "headline_style": "...", "subtitle_style": "...", "text_accuracy": "..." },
  "reference_images": { "total_count": 3, "user_images": [], "safe_zone_guide": {} },
  "panels": [ { "panel": 1, "position": "left third", "headline": "...", "..." : "..." } ],
  "constraints": [ "Keep text at least 80px from crop edges...", "..." ]
}
```

### Server-Injected Fields

These fields are controlled by the server — the agent does **not** send them:

| Field | Source |
|-------|--------|
| `task` | Built from app name + canvas dimensions |
| `format` | Computed from platform + panel count |
| `campaign.app_name`, `campaign.developer`, `campaign.app_description`, `campaign.listing` | App record in database |
| `campaign.platform`, `campaign.target_locale` | Generation request |
| `reference_images` | Resolved from `referenceAssetIds` + `referenceScreenshotIds` |
| `reference_images.safe_zone_guide` | Always appended as last reference image |
| Panel `position` | Computed from platform + panel index (e.g. "left third", "center third") |
| `constraints` | Platform-specific margin, Apple Guideline 2.3.3, safe-zone rules |

### Agent Schema

The agent sends these fields via `generate_screenshots`:

```
campaign:
  goal            — Campaign goal, e.g. "Drive downloads from search"
  category        — App category override (optional, defaults to app record)
  audience        — Target audience
  core_promise    — Core value proposition
  differentiator  — Key differentiator from competitors

visual_direction:
  style_family    — e.g. "clean-premium", "bold-playful", "minimal-mono"
  mood            — e.g. "dark, confident, aspirational"
  palette         — { primary, secondary, accent, text, subtitle }
  motifs          — Recurring visual elements, e.g. ["teal gradient orbs", "grain texture"]
  devices         — Device treatment, e.g. "Every panel features iPhone 15 Pro"
  notes           — Free-form visual notes

typography:       (server provides sensible defaults if omitted)
  headline_style  — e.g. "Large, bold, sans-serif, white, high-contrast"
  subtitle_style  — e.g. "Smaller, regular weight, light gray"

panels[]:         (one per panel, count must match platform)
  headline        — Exact headline text (isolated for text accuracy)
  subtitle        — Exact subtitle text
  description     — Free-form catch-all (use alone or with structured fields)
  role            — What this panel communicates
  layout          — Layout instructions
  device          — Single device: { model, frame, screen, tilt }
  devices         — Multiple devices: [{ position, model, screen }]
  background      — Background description
  breakout_elements — Floating UI fragments, thematic objects
  references      — Per-panel reference image mapping: [{ image: 1, usage: "..." }]
  typography      — Per-panel typography override
  visual_direction — Per-panel visual direction override
```

### Panel Examples

#### Device-led panel (single device)

```json
{
  "headline": "Know What Actually Works",
  "subtitle": "50+ expert dating guides",
  "role": "Show the Learn tab with dating guides",
  "device": {
    "model": "iPhone 15 Pro",
    "screen": "Learn tab with article cards, dark theme, teal accent",
    "tilt": "5° clockwise"
  },
  "background": "Dark charcoal with soft teal gradient glow behind device",
  "references": [
    { "image": 1, "usage": "Reproduce this exact Learn tab layout on the device screen" }
  ]
}
```

#### Device-led panel (multi-device)

```json
{
  "headline": "37 Scenes. Pick Your Story.",
  "subtitle": "Dinner. Travel. Fitness. More.",
  "role": "Show variety of photo packs",
  "layout": "Headline top 20%, two iPhones below, opposing 3-5° tilts",
  "devices": [
    { "position": "left", "screen": "Browse scenes grid - dark theme" },
    { "position": "right", "screen": "Scene detail - candlelit dinner" }
  ],
  "background": "Dark charcoal #0F1416 with soft teal radial glow",
  "breakout_elements": "Floating scene thumbnails with drop shadows"
}
```

#### Text-led panel (no device)

```json
{
  "headline": "Real Convos. Real Wins.",
  "subtitle": "Know what to say",
  "role": "Show conversation breakdowns and coaching",
  "layout": "headline-dominant, no device",
  "background": "Dark charcoal with subtle teal accent lighting from below"
}
```

## Conversion Defaults

- panel 1 interrupts the category's default expectation
- panel 2 proves the app with concrete UI
- panel 3 closes with confidence, trust, or outcome
- one promise per panel
- if the category is crowded, make the hero panel more specific, not louder
- headlines should create recognition, tension, or a clear outcome
- subtitles should support the headline by reframing, calming doubt, or granting permission
- match the visual style to the app's brand and category (see Visual Style table)

## Device Content

Good:

- `A ranking screen with three profile photo cards, a mint 9.7 badge, score rows for Lighting, Expression, and Composition, and a black pill button that says ANALYZE`

Bad:

- `Shows the app`

If real screenshots exist, idealize them rather than inventing unrelated screens.

## Reference Image Usage

When reference images are available, call them out explicitly in the prompt:

```text
Reference image usage:
- Use reference image 1 for the real app UI layout and visual hierarchy.
- Use reference image 2 for brand color and icon style.
- Use reference image 3 only as mood/inspiration, not literal UI.
```

Never say only "use references"; specify what each reference should influence.

## Safe-Zone Reference Image (Optional)

For tighter crop safety, pass a reference image at the composite dimensions
that visually marks safe zones and bleed zones:

- **Safe zones**: lighter rectangles inset 120px from each panel's left/right
  crop edges — all text and text-bearing breakout elements go here
- **Bleed zones**: tinted margins near panel crop boundaries — fill with
  background gradients and decorative elements only, never text or UI cards

When using a safe-zone reference, add to the prompt:
"A reference image is provided showing safe zones (lighter areas) and bleed
zones (tinted margins near panel edges). Place all text and text-bearing
breakout elements within the safe zones only. Fill bleed zones with
background color and decorative elements."

## Platform-Specific Rules

### Panel count limits

| Platform | Max panels | Composite layout |
| --- | --- | --- |
| iPhone | 3 | vertical strip |
| iPad | 2 | vertical strip |
| Android | 3 | vertical strip |
| Watch | 4 | 2×2 grid |

The MCP server enforces these limits by splitting the `screenshots` array into multiple jobs when needed.

### iPhone (1290×2796)

- Default platform. Use "iPhone 15 Pro" or "iPhone 16 Pro" device frames.
- Server composite: `1120*panelCount × 2432` (e.g. 3 panels → 3360×2432).

### iPad (2064×2752)

- Wider columns mean more horizontal space per panel.
- Server composite: `1920*panelCount × 2560` (e.g. 2 panels → 3840×2560).
- Use "iPad Pro" device frames — larger bezels, bigger device. Scale up UI elements and text to fill the wider panel.
- Landscape device orientation often works better for iPad since many iPad apps run landscape.
- Headlines and subtitles need to be proportionally larger than iPhone to avoid looking lost in the wider panel.

### Android (1080×1920)

- Server composite: `1082*panelCount × 1920` (e.g. 3 panels → 3246×1920).
- Aspect ratio is 9:16 (shorter and wider than iPhone's ~9:19.5).
- Use Android device language: "Pixel 9 Pro" or "Samsung Galaxy S25", punch-hole camera (not notch), Material Design UI.
- Do NOT say "iPhone", "notch", "Dynamic Island", or iOS-specific terms in the prompt.
- Android screenshots appear in Google Play, which has a different browse layout — optimize for the Google Play grid.

### Watch (416×496)

- Server composite is always `1920×1920` for a 4-screenshot watch job. The 4 panels are arranged in a **2×2 grid**, not a vertical strip.
- For individual screenshot prompts, do not say "4 equal vertical panels".
- Each cell crops to 416×496 (roughly 5:6 aspect ratio).
- Tiny screens demand minimal text: one short headline per cell, no subtitles. 3-5 words max.
- No device frames — watch screenshots are shown without bezels.
- Use large, bold iconography and high-contrast colors. Fine detail is invisible at watch scale.

### Composite dimensions quick reference

Use these exact values in the `{width}x{height}` template slot:

| Platform | 1 panel | 2 panels | 3 panels | 4 panels |
| --- | --- | --- | --- | --- |
| iPhone | 1120×2432 | 2240×2432 | 3360×2432 | — |
| iPad | 1920×2560 | 3840×2560 | — | — |
| Android | 1082×1920 | 2164×1920 | 3246×1920 | — |
| Watch | — | — | — | 1920×1920 |

## Full Prompt Examples

### iPhone — structured input

```json
{
  "campaign": {
    "goal": "maximize thumb-stop attention and install intent",
    "category": "Productivity",
    "audience": "remote workers and students who struggle with distraction",
    "core_promise": "deep focus sessions that adapt to your energy"
  },
  "visual_direction": {
    "style_family": "clean-premium",
    "mood": "calm, focused, confident",
    "palette": { "primary": "#1A1A2E", "secondary": "#16213E", "accent": "#E94560", "text": "#FFFFFF" }
  },
  "panels": [
    {
      "headline": "Focus That Adapts to You",
      "subtitle": "AI-powered deep work sessions",
      "role": "stop the scroll with a bold promise",
      "layout": "top-center text, centered device",
      "device": {
        "model": "iPhone 15 Pro",
        "frame": "jet-black, thin bezels, Dynamic Island",
        "screen": "a live focus timer at 23:41, a pulsing ring animation, session streak counter showing '12 days', and a green 'In the Zone' status badge"
      },
      "background": "deep navy gradient with subtle radial glow behind the device"
    }
  ]
}
```

### Android — structured input

```json
{
  "campaign": {
    "category": "Finance",
    "audience": "young professionals managing their first budget",
    "core_promise": "spending insights that actually change your habits"
  },
  "visual_direction": {
    "style_family": "clean Material Design, friendly fintech",
    "palette": { "primary": "#0D1B2A", "secondary": "#1B263B", "accent": "#00C896", "text": "#FFFFFF" }
  },
  "panels": [
    {
      "headline": "Know Before You Spend",
      "subtitle": "Smart nudges. Real savings.",
      "layout": "top-center text, centered device",
      "device": {
        "model": "Pixel 9 Pro",
        "frame": "matte black, punch-hole camera top center",
        "screen": "a notification card reading 'Coffee budget 80% used - 6 days left' over a dashboard with a weekly spending ring at 72%, three category rows, and a green 'On Track' badge"
      },
      "background": "dark navy gradient with a subtle upward curve of green light at the bottom"
    }
  ]
}
```

## Revision Pattern

Append this to the original prompt for revisions:

```text
REVISION INSTRUCTIONS

Change:
- {exact requested change}

Preserve:
- positioning, panel order, crop safety
- the existing theme, palette, and typography unless explicitly changed
- unchanged device screens, copy, and background elements

Constraints:
- keep everything else the same
```
