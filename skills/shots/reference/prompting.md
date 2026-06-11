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
- headlines must follow the Screenshot Title Strategy in `strategy.md`: 3-6 word benefit, relief, identity, curiosity, or transformation promises
- do not use feature-label headlines when a benefit rewrite is possible
- device screens must show specific UI
- decorative elements must support the promise, not distract from it
- optimize for browse-feed stopping power before full-size polish
- treat panel 1 like it may be the only panel the user really notices
- existing screenshots are visual reference, not copy reference
- selected/promoted English screenshots are the preferred campaign style reference otherwise use your best judgement based on what you know about the project and the style of the app inside the repo by analyzing the main screens and theme files and what would fit best for an app of its kind
- prior screenshot prompts should inform palette, typography, composition, background language, and device treatment when available
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

## Device Proportions

The rendered device must look like the real physical hardware — correct width-to-height ratio, not stretched to fill the canvas.

| Platform | Body ratio (h:w) | Width on canvas | Height on canvas | Canvas height % |
|----------|-------------------|-----------------|------------------|-----------------|
| iPhone   | ~2.07:1           | 700–820 px      | 1450–1700 px     | 53–62%          |
| Android  | ~2.15:1           | 680–800 px      | 1460–1720 px     | 53–63%          |
| iPad     | ~1.43:1           | 800–1000 px     | 1140–1430 px     | 42–52%          |

- The device should occupy ~50-60% of canvas height for phones, leaving room for headline above and breathing room below.
- Never stretch the device to fill the full canvas height.
- When writing the `layout` field, include approximate device dimensions (e.g., `"~780px wide, ~1615px tall, occupying ~59% of canvas height"`).

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

## Prompt Format

Send a complete prompt to `generate_screenshot` via the `prompt` field. JSON format works best with GPT Image 2, but natural language is also accepted.

The server appends dimensions, constraints, and reference image metadata. Everything else — campaign context, visual direction, typography, device content, layout — is up to you.

### Recommended JSON sections

- `task` — what to create (e.g. `"Create one App Store screenshot for \"AppName\"."`)
- `campaign` — goal, audience, core_promise, differentiator
- `typography` — headline_style, subtitle_style, text_accuracy
- `visual_direction` — style_family, mood, palette (primary/secondary/accent/text/subtitle), motifs, devices, notes
- `screenshot` — headline, subtitle, role, layout, device (model/frame/screen/tilt), background, breakout_elements, negative_constraints

### Example 1: Ethereal cosmic app (health/journal)

```json
{
  "task": "Create one App Store screenshot for \"Lucid: AI Dream Journal\".",
  "campaign": {
    "goal": "maximize thumb-stop attention and install intent",
    "category": "Health & Wellness / Journaling",
    "audience": "Adults 20-35 curious about their dreams, interested in self-reflection",
    "core_promise": "Never forget a dream again — AI captures, illustrates, and finds patterns",
    "differentiator": "Only dream journal that generates visual scenes from descriptions and tracks emotional patterns"
  },
  "typography": {
    "headline_style": "Large, bold, white (#FFFFFF) with subtle luminous glow, high-contrast, thumbnail-readable.",
    "subtitle_style": "Smaller, soft lavender (#C4A1FF), gentle and supportive.",
    "text_accuracy": "Render all quoted text verbatim. >95% accuracy."
  },
  "visual_direction": {
    "style_family": "ethereal-cosmic",
    "mood": "dreamy, ethereal, mysterious, wonder — luminous and inviting, never dark or scary",
    "palette": {
      "primary": "#0B0E2C (midnight indigo)",
      "secondary": "#2D1B69 (rich violet)",
      "accent": "#7DF9FF (soft cyan) + #C4A1FF (lavender)",
      "text": "#FFFFFF (white)",
      "subtitle": "#C4A1FF (lavender)"
    },
    "motifs": ["aurora-like light ribbons in cyan and lavender", "floating crescent moon with soft glow", "tiny star particles", "translucent dream scene thumbnails with soft borders"],
    "devices": "iPhone 15 Pro",
    "notes": "The mood must be ethereal and wonder-filled — like the moment between sleep and waking"
  },
  "screenshot": {
    "headline": "Remember Every Dream",
    "subtitle": "AI that turns fragments into stories",
    "role": "hero — interrupts the scroll with wonder and curiosity",
    "layout": "headline and subtitle at top ~20%, floating iPhone 15 Pro (~780px wide, ~1615px tall, ~59% of canvas height) below center with very slight tilt",
    "device": {
      "model": "iPhone 15 Pro",
      "frame": "silver titanium, thin bezels, Dynamic Island",
      "screen": "Dream journal feed — dark background (#0E1029). Greeting 'Good morning, Alex' with crescent moon icon. Dream card 'The Violet Ocean' with AI-generated purple ocean thumbnail, tagged 'Lucid Dream · 4:23 AM', sentiment bar 'Wonder 82% · Calm 15%'. Second card 'The Glass Library'. Bottom tabs: Journal, Patterns, Record, Insights, Profile.",
      "tilt": "very slight — dreamy and weightless"
    },
    "background": "Deep gradient from midnight indigo (#0B0E2C) through rich violet (#2D1B69) to deep navy (#1A1050), faint aurora ribbons of cyan and lavender in upper third, scattered star particles",
    "breakout_elements": "Translucent crescent moon floating upper-left with drop shadow (6px offset, 16px blur, 35% opacity), 2-3 dream scene thumbnails drifting away from device at slight rotation (5°) with soft purple/cyan borders, wispy particle trails connecting them back to screen",
    "negative_constraints": "No badges, no app store buttons, no fake UI chrome, no Lorem Ipsum, no text clipped at edges, no borders or dividers"
  }
}
```

### Example 2: Dark premium app (dating/photos)

```json
{
  "task": "Create one App Store screenshot for \"Pull\".",
  "campaign": {
    "goal": "Showcase AI photo enhancement for dating profiles",
    "audience": "Young adults 18-30 who want better dating app photos",
    "core_promise": "AI-enhanced photos that get more matches",
    "differentiator": "Uses AI to subtly enhance photos while keeping them authentic"
  },
  "typography": {
    "headline_style": "Large, bold, white sans-serif with slight shadow for depth, high-contrast.",
    "subtitle_style": "Smaller, light gray (#A0A0A0), clean and modern.",
    "text_accuracy": "Render all quoted text verbatim. >95% accuracy."
  },
  "visual_direction": {
    "style_family": "clean-premium",
    "mood": "dark, confident, aspirational — premium but approachable",
    "palette": {
      "primary": "#0A0A0F (near-black)",
      "secondary": "#1A1A2E (dark navy)",
      "accent": "#FF6B6B (warm coral)",
      "text": "#FFFFFF (white)",
      "subtitle": "#A0A0A0 (light gray)"
    },
    "motifs": ["subtle gradient overlays", "soft bokeh light spots", "clean geometric framing"],
    "devices": "iPhone 16 Pro",
    "notes": "Premium but not cold — warm accent colors keep it inviting"
  },
  "screenshot": {
    "headline": "Photos That Get Replies",
    "subtitle": "AI enhancement that looks natural",
    "role": "hero — proves transformation with before/after evidence",
    "layout": "headline at top, centered iPhone 16 Pro (~760px wide, ~1575px tall, ~58% of canvas height) below with before/after comparison on screen",
    "device": {
      "model": "iPhone 16 Pro",
      "frame": "black titanium",
      "screen": "Split-screen comparison: left side shows original photo (good but flat lighting), right side shows AI-enhanced version (better color, subtle skin smoothing, improved lighting). Toggle switch at top labeled 'Original / Enhanced'. Like count showing '3x more likes' badge.",
      "tilt": "straight-on, slight float"
    },
    "background": "Rich dark gradient from #0A0A0F to #1A1A2E with subtle warm coral (#FF6B6B) glow behind device",
    "breakout_elements": "Small floating heart/like notification badges around device edges with drop shadow (6px offset, 16px blur, 30% opacity) and slight rotation (4°), subtle sparkle particles near the enhanced side",
    "negative_constraints": "No badges, no app store buttons, no fake UI chrome, no Lorem Ipsum, no text clipped at edges"
  }
}
```

### Example 3: Clean productivity app

```json
{
  "task": "Create one App Store screenshot for \"FocusFlow\".",
  "campaign": {
    "goal": "maximize thumb-stop attention and install intent",
    "category": "Productivity",
    "audience": "remote workers and students who struggle with distraction",
    "core_promise": "deep focus sessions that adapt to your energy"
  },
  "typography": {
    "headline_style": "Large, bold, white sans-serif, high-contrast, thumbnail-readable.",
    "subtitle_style": "Smaller, regular weight, light gray.",
    "text_accuracy": "Render all quoted text verbatim. >95% accuracy."
  },
  "visual_direction": {
    "style_family": "clean-premium",
    "mood": "calm, focused, confident",
    "palette": {
      "primary": "#1A1A2E",
      "secondary": "#16213E",
      "accent": "#E94560",
      "text": "#FFFFFF"
    }
  },
  "screenshot": {
    "headline": "Focus That Adapts to You",
    "subtitle": "AI-powered deep work sessions",
    "role": "hero — interrupts the scroll with a bold promise",
    "layout": "top-center text, centered iPhone 15 Pro (~780px wide, ~1615px tall, ~59% of canvas height) below",
    "device": {
      "model": "iPhone 15 Pro",
      "frame": "jet-black, thin bezels, Dynamic Island",
      "screen": "a live focus timer at 23:41, a pulsing ring animation, session streak counter showing '12 days', and a green 'In the Zone' status badge"
    },
    "background": "deep navy gradient with subtle radial glow behind the device",
    "negative_constraints": "No badges, no app store buttons, no fake UI chrome, no Lorem Ipsum, no text clipped at edges"
  }
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
- before sending prompts, rewrite weak feature titles into stronger outcomes, e.g. "AI Meal Planner" -> "Know Dinner by 5", "Budgeting Dashboard" -> "Stop Wondering Where It Went", "Calendar Sync" -> "See Your Day Clearly"

## Device Content

When the App Experience research includes a Critical Screen description for the screen being shown, use it as the basis for the `device.screen` field. Reference the exact layout structure, UI elements, data, and interactive states from the screen description rather than inventing generic descriptions. This produces device content that matches the real app.

Good:

- `A ranking screen with three profile photo cards, a mint 9.7 badge, score rows for Lighting, Expression, and Composition, and a black pill button that says ANALYZE`
- (From Critical Screen research) `Home dashboard with nav bar, search field, 3 filter chips (All, Active, Completed), scrollable card grid showing 4 habit cards with circular progress rings, streak counters, and category color bars, bottom tab bar with 4 icons`

Bad:

- `Shows the app`
- `A clean dashboard with some cards` (too vague — use the Critical Screen description for specifics)

If real screenshots exist, idealize them rather than inventing unrelated screens.

If NO real screenshots or UI references exist, do not generate. Ask the user to provide at least one real app screenshot first. Apple requires screenshots to depict actual app usage — inventing fictional UI risks App Store rejection.

## Reference Image Usage

When reference images are available, call them out explicitly in the prompt:

```text
Reference image usage:
- Use reference image 1 for the real app UI layout and visual hierarchy.
- Use reference image 2 for the existing English campaign style: palette, typography, composition, device treatment, background language, and overall vibe.
- Use reference image 3 for brand color and icon style.
- Use reference image 4 only as mood/inspiration, not literal UI.
```

Never say only "use references"; specify what each reference should influence.

When selected/promoted or saved English screenshots exist, include at least one as a style reference in `referenceMediaIds`. Keep the real app UI screenshot as the product-accuracy reference. Existing generated screenshots keep the campaign consistent; they do not replace real app UI references, and their old headline/subtitle copy should not be reused unless the user explicitly asks.

## Platform Notes

The server computes dimensions automatically.

- **iPhone** — Default platform. Use "iPhone 15 Pro" or "iPhone 16 Pro" device frames. Body aspect ratio ~2.07:1 (h:w). Size the device at ~55-65% of canvas width, ~53-62% of canvas height (~700-820px wide, ~1450-1700px tall on a 1264x2736 canvas). The device must not stretch to fill the canvas height.
- **iPad** — Wider panels, scale up text and UI. Landscape orientation often works better. Use "iPad Pro" device frames. Body aspect ratio ~1.43:1 (h:w). Size the device at ~65-80% of canvas width, ~42-52% of canvas height.
- **Android** — Use "Pixel 9 Pro" or "Samsung Galaxy S25", punch-hole camera, Material Design UI. Do NOT say "iPhone", "notch", "Dynamic Island", or iOS-specific terms. Body aspect ratio ~2.15:1 (h:w). Size the device at ~55-65% of canvas width, ~53-63% of canvas height.
- **Watch** — Tiny screens: 3-5 word headlines, no subtitles, no device frames. Use large bold iconography and high-contrast colors.

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
