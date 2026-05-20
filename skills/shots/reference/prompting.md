# Shots — GPT Image 2 Prompting Contract

Turn the saved `strategyBrief` and chosen `benefits` into one crop-safe composite prompt.

## Objective

Create one wide App Store composite that becomes three standalone ads after cropping. Optimize for small-screen clarity and install intent first.

Shots generates a composite image and then crops it into individual App Store screenshots. Do not ask the model for separate framed screenshots inside one image. Do not add rounded panel corners, panel borders, gutters, dividers, or mock App Store chrome. The crop itself supplies the final screenshot boundary.

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
- generate exactly the approved screenshot count, batched into composites of up to the platform's max panel count (see Platform-Specific Rules)
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

### 3D & Pop-Out Depth

Every device-led panel should feel like it's bursting out of the screen. Build compositions with three explicit depth layers (back → front):

1. **Background layer** — gradient orbs, abstract shapes, or atmospheric color washes. Use at least two overlapping soft-edged shapes. Never a flat solid color.
2. **Device layer** — the phone/tablet frame sits in the mid-ground, anchoring the composition.
3. **Foreground breakout layer** — UI fragments, cards, icons, or thematic objects that float *in front* of the device, overlapping or breaking the bezel plane.

**Breakout element rules:**
- Max 1-2 breakout elements per panel — more feels cluttered
- Each element gets a drop shadow (offset ~6 px, blur ~16 px, 30-40% opacity black)
- Slight rotation (3-8°) adds life — avoid perfectly axis-aligned floaters
- Elements must relate to the actual app UI (a real card, stat, icon, or control), not generic decoration
- Breakout elements may overlap the device bezel — this is intentional and creates the pop-out effect
- Text-bearing breakout elements (cards with labels, badges with copy) must sit fully inside the panel's crop zone — never clipped at crop edges. Non-text decorative elements (gradient orbs, shapes, device edges) may bleed across crop boundaries for visual continuity.

**Background depth rules:**
- At least two overlapping gradient orbs or abstract shapes behind the device
- Soft-edged, large scale — they set mood, not demand attention
- Creates parallax: background shapes → device → breakout elements
- Never use a flat solid color behind a device

**Overall vibe:** bold, dynamic, and personality-driven. Compositions should feel like they have energy and motion, not like a static product render.

## Panoramic Background (Optional)

For cohesive multi-panel narratives, use a single wide background:

"Design a continuous gradient background transitioning from [color1] on the left to [color2] on the right, with [geometric element] spanning across the full composite. The three final panels should be evenly spaced crops at the selected platform dimensions, creating visual continuity and a cut-off effect that encourages scrolling."

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

"No badges, no app store buttons, no fake UI chrome, no generic stock photo people, no Lorem Ipsum text, no flat solid-color backgrounds, no compositions where all elements sit inside the device bezel, no text clipped at panel crop boundaries"

### Example Full Prompt

"A vibrant, modern app store marketing screenshot featuring an iPhone 15 Pro displaying the Meditation Timer screen with the headline 'Find Your Calm in 5 Minutes' in bold Helvetica Neue, white text with slight shadow for contrast. The device is positioned in the left third, angled 15° clockwise, with a serene gradient background from soft lavender (#E6E6FA) to pale blue (#B0C4DE). Floating abstract lotus petal shapes in the background create depth without distraction. The app screen shows a circular timer interface with purple accent color (#7B68EE), surrounded by subtle glow. Clean, professional, and inviting aesthetic. No badges, no borders, no fake app store elements."

## Prompt Template

```text
Create one {width}x{height} App Store marketing composite for "{appName}" with {panelCount} equal vertical panels and no visible seams.

Campaign:
- Goal: maximize thumb-stop attention and install intent
- Category: {category}
- Audience: {targetAudience}
- Core promise: {corePromise}
- Differentiator: {differentiator}

Visual direction:
- Style family: {styleFamily}
- Mood: {mood}
- Palette: primary {primary}, secondary {secondary}, accent {accent}, text {text}
- Motifs: {motif1}, {motif2}, {motif3}

Typography:
- Headline is large, bold, high-contrast, and readable in App Store browse surfaces
- Subtitle is smaller and supportive
- Render all quoted text verbatim
- Use quotation marks for exact copy to ensure >95% text accuracy

Panels:
{panel blocks}

Constraints:
- each panel crops cleanly to {targetWidth}x{targetHeight}
- keep headline and subtitle text at least 80px from crop edges
- breakout elements containing readable text must sit fully inside the crop zone — not clipped at panel boundaries. Decorative non-text elements may bleed across boundaries.
- no borders, dividers, seams, gutters, rounded crop corners, App Store badges,
  download buttons, or fake marketplace chrome
- device screens must show believable UI, never blank placeholders
```

## Panel Blocks

### Device-led panel

```text
Panel {n} ({arcPosition}):
- EXACT HEADLINE: "{headline}"
- EXACT SUBTITLE: "{subtitle}"
- Role: {what the panel must prove}
- Layout: {textPosition} text, centered device
- Device: {model} {frame}, {bezels}, {camera}. The device should span about 75% of the panel width with even margins on each side.
- Screen shows: {specific UI content with exact cards, rows, charts, buttons, and example values}
- Breakout elements (REQUIRED — max 2): {1-2 UI fragments, cards, or icons floating in front of the device, breaking the bezel plane, with drop shadows (offset ~6px, blur ~16px) and slight rotation (3-8°)}
- Background depth (REQUIRED): {2-3 layered gradient orbs or abstract shapes behind the device. Never a flat solid color. Creates parallax: background shapes → device → breakout elements.}
```

### Text-led panel

```text
Panel {n} ({arcPosition}):
- EXACT HEADLINE: "{headline}"
- EXACT SUBTITLE: "{subtitle}"
- Role: {why the panel exists}
- Layout: headline-dominant, no device
- Hero visual: {optional object, mascot, cutout, or stat}
- Background depth: {theme-specific layers}
```

## Conversion Defaults

- panel 1 interrupts the category's default expectation
- panel 2 proves the app with concrete UI
- panel 3 closes with confidence, trust, or outcome
- one promise per panel
- if the category is crowded, make the hero panel more specific, not louder
- headlines should create recognition, tension, or a clear outcome
- subtitles should support the headline by reframing, calming doubt, or granting permission
- every device-led panel must include breakout elements and background depth (no flat compositions)

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

Never exceed these limits. A 4-panel iPhone job will fail.

### iPhone (1290×2796)

- Default platform. Use "iPhone 15 Pro" or "iPhone 16 Pro" device frames.
- Composite: `1120*panelCount × 2432` (e.g. 3 panels → 3360×2432).
- Up to 3 panels per job.

### iPad (2064×2752)

- Only 2 panels per job. Wider columns mean more horizontal space per panel.
- Composite: `1920*panelCount × 2560` (e.g. 2 panels → 3840×2560).
- Use "iPad Pro" device frames — larger bezels, bigger device. Scale up UI elements and text to fill the wider panel.
- Landscape device orientation often works better for iPad since many iPad apps run landscape.
- Headlines and subtitles need to be proportionally larger than iPhone to avoid looking lost in the wider panel.

### Android (1080×1920)

- Composite: `1082*panelCount × 1920` (e.g. 3 panels → 3246×1920).
- Up to 3 panels per job. Aspect ratio is 9:16 (shorter and wider than iPhone's ~9:19.5).
- Use Android device language: "Pixel 9 Pro" or "Samsung Galaxy S25", punch-hole camera (not notch), Material Design UI.
- Do NOT say "iPhone", "notch", "Dynamic Island", or iOS-specific terms in the prompt.
- Android screenshots appear in Google Play, which has a different browse layout — optimize for the Google Play grid.

### Watch (416×496)

- Composite is always `1920×1920` regardless of panel count. The 4 panels are arranged in a **2×2 grid**, not a vertical strip.
- In the prompt say "2×2 grid (4 cells)" — NOT "4 equal vertical panels".
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

## Full Platform Examples

### iPhone 3-panel example

```text
Create one 3360x2432 App Store marketing composite for "FocusFlow" with 3 equal vertical panels and no visible seams.

Campaign:
- Goal: maximize thumb-stop attention and install intent
- Category: Productivity
- Audience: remote workers and students who struggle with distraction
- Core promise: deep focus sessions that adapt to your energy
- Differentiator: AI-powered session lengths based on focus patterns

Visual direction:
- Style family: minimal gradient, modern tech
- Mood: calm, focused, confident
- Palette: primary #1A1A2E, secondary #16213E, accent #E94560, text #FFFFFF
- Motifs: concentric focus rings, soft glow pulses, ambient particles

Typography:
- Headline is large, bold, high-contrast, and readable in App Store browse surfaces
- Subtitle is smaller and supportive
- Render all quoted text verbatim
- Use quotation marks for exact copy to ensure >95% text accuracy

Panels:
Panel 1 (hero):
- EXACT HEADLINE: "Focus That Adapts to You"
- EXACT SUBTITLE: "AI-powered deep work sessions"
- Role: stop the scroll with a bold promise
- Layout: top-center text, centered device
- Device: iPhone 15 Pro, jet-black frame, thin bezels, Dynamic Island
- Screen shows: a live focus timer at 23:41 with a pulsing ring animation, session streak counter showing "12 days", and a green "In the Zone" status badge
- Breakout elements: glowing focus ring expanding beyond the device edge with soft particle trail
- Background depth: deep navy gradient with subtle radial glow behind the device

Panel 2 (proof):
- EXACT HEADLINE: "Your Pattern, Your Pace"
- EXACT SUBTITLE: "Sessions tuned to your rhythm"
- Role: prove the AI adapts with concrete data
- Layout: left-aligned text, right-side device
- Device: iPhone 15 Pro, jet-black frame, thin bezels, Dynamic Island
- Screen shows: a weekly focus chart with colored bars for Mon-Sun, average session length "47 min", a trend arrow up 18%, and three recommended session cards (Morning Sprint 25m, Deep Dive 52m, Evening Wind-Down 15m)
- Breakout elements: one session card floating beside the device with a drop shadow
- Background depth: dark navy base with a soft horizontal light band across the middle

Panel 3 (close):
- EXACT HEADLINE: "Start in Seconds"
- EXACT SUBTITLE: "No setup. Just focus."
- Role: reduce friction and invite action
- Layout: centered text above device
- Device: iPhone 15 Pro, jet-black frame, thin bezels, Dynamic Island
- Screen shows: the home screen with a single large "Begin Focus" button in accent red, a minimalist session picker (25 / 45 / 90 min), and a subtle "Free for 14 days" label below
- Breakout elements: a soft upward glow emanating from the button
- Background depth: gradient lifting from dark base to slightly lighter navy at top

Constraints:
- each panel crops cleanly to 1290x2796
- keep text at least 80px from crop edges
- no borders, dividers, seams, gutters, rounded crop corners, App Store badges,
  download buttons, or fake marketplace chrome
- device screens must show believable UI, never blank placeholders
```

### iPad 2-panel example

```text
Create one 3840x2560 App Store marketing composite for "Sketcher Pro" with 2 equal vertical panels and no visible seams.

Campaign:
- Goal: showcase the large-canvas drawing experience
- Category: Graphics & Design
- Audience: digital artists and illustrators who use iPad as their primary canvas
- Core promise: pro-grade drawing tools designed for the big screen
- Differentiator: pressure-sensitive brushes with zero-latency ink engine

Visual direction:
- Style family: bold creative, gallery-inspired
- Mood: expressive, professional, artistic
- Palette: primary #1C1C1E, secondary #2C2C2E, accent #FF9F0A, text #FFFFFF
- Motifs: brush strokes, paint splashes, canvas textures

Typography:
- Headline is large, bold, high-contrast — scale up for iPad's wider panels
- Subtitle is smaller and supportive
- Render all quoted text verbatim
- Use quotation marks for exact copy to ensure >95% text accuracy

Panels:
Panel 1 (hero):
- EXACT HEADLINE: "Your Canvas, Unlimited"
- EXACT SUBTITLE: "Pro brushes with zero-latency ink"
- Role: show the scale advantage of iPad drawing
- Layout: top-center text, centered device in landscape
- Device: iPad Pro 12.9", space gray, thin bezels, landscape orientation
- Screen shows: a detailed digital illustration in progress — a mountain landscape with visible brush strokes, a floating brush toolbar on the left edge, color wheel in the bottom-right corner, and layer panel showing 12 layers
- Breakout elements: a paint brush with an orange stroke trailing off the device edge, paint droplets with soft shadows
- Background depth: charcoal textured canvas background with subtle warm spotlight behind the device

Panel 2 (proof):
- EXACT HEADLINE: "200+ Pro Brushes"
- EXACT SUBTITLE: "Pencil, ink, oil, watercolor — all pressure-sensitive"
- Role: prove the tool depth with tangible examples
- Layout: left-aligned text, right-side device in landscape
- Device: iPad Pro 12.9", space gray, thin bezels, landscape orientation
- Screen shows: the brush library open with a 4-column grid of brush previews (pencil, charcoal, flat oil, round watercolor, splatter ink, soft airbrush), each with a sample stroke beside it, and a "Favorites" tab highlighted at the top
- Breakout elements: three sample brush strokes floating beside the device — one pencil texture, one watercolor wash, one bold ink line — each with drop shadows
- Background depth: dark base with a faint orange radial glow in the upper right

Constraints:
- each panel crops cleanly to 2064x2752
- keep text at least 100px from crop edges — iPad panels are wider so increase margin
- no borders, dividers, seams, gutters, rounded crop corners, App Store badges,
  download buttons, or fake marketplace chrome
- device screens must show believable UI, never blank placeholders
```

### Android 3-panel example

```text
Create one 3246x1920 Google Play marketing composite for "BudgetBuddy" with 3 equal vertical panels and no visible seams.

Campaign:
- Goal: maximize install rate on Google Play browse grid
- Category: Finance
- Audience: young professionals managing their first budget
- Core promise: spending insights that actually change your habits
- Differentiator: auto-categorization with smart nudges before you overspend

Visual direction:
- Style family: clean Material Design, friendly fintech
- Mood: trustworthy, approachable, modern
- Palette: primary #0D1B2A, secondary #1B263B, accent #00C896, text #FFFFFF
- Motifs: rounded cards, subtle coin/wallet iconography, upward trend lines

Typography:
- Headline is large, bold, high-contrast, and readable in Google Play browse surfaces
- Subtitle is smaller and supportive
- Render all quoted text verbatim
- Use quotation marks for exact copy to ensure >95% text accuracy

Panels:
Panel 1 (hero):
- EXACT HEADLINE: "Know Before You Spend"
- EXACT SUBTITLE: "Smart nudges. Real savings."
- Role: stop the scroll with a relatable pain point
- Layout: top-center text, centered device
- Device: Pixel 9 Pro, matte black frame, thin bezels, punch-hole camera top center
- Screen shows: a notification card reading "Coffee budget 80% used — 6 days left" overlaying a clean dashboard with a weekly spending ring chart at 72%, three category rows (Food $142, Transport $68, Entertainment $35), and a green "On Track" badge
- Breakout elements: the notification card floating slightly above the device with a soft green glow and drop shadow
- Background depth: dark navy gradient with a subtle upward curve of green light at the bottom

Panel 2 (proof):
- EXACT HEADLINE: "Every Dollar, Sorted"
- EXACT SUBTITLE: "Auto-categorized in real time"
- Role: prove the auto-categorization with concrete UI
- Layout: right-aligned text, left-side device
- Device: Pixel 9 Pro, matte black frame, thin bezels, punch-hole camera top center
- Screen shows: a transaction list with Material Design cards — each row has a category icon (coffee cup, gas pump, shopping bag), merchant name, amount, and a colored category chip. A "Smart Sort" toggle is enabled at the top with a sparkle icon
- Breakout elements: one transaction card (Starbucks – Coffee – $5.40) floating beside the device with a slight tilt and shadow
- Background depth: dark base with soft teal horizontal light bands

Panel 3 (close):
- EXACT HEADLINE: "Free to Start"
- EXACT SUBTITLE: "Set up in 2 minutes"
- Role: remove friction and invite download
- Layout: centered text above device
- Device: Pixel 9 Pro, matte black frame, thin bezels, punch-hole camera top center
- Screen shows: the onboarding screen with a large "Connect Your Bank" Material Design button in green, a progress stepper showing step 1 of 3, and a "Skip for now" text link below
- Breakout elements: a soft upward glow from the button, a small shield icon floating nearby suggesting security
- Background depth: gradient lifting from dark base to slightly lighter navy

Constraints:
- each panel crops cleanly to 1080x1920
- keep text at least 60px from crop edges
- no borders, dividers, seams, gutters, rounded crop corners, Google Play badges,
  install buttons, or fake marketplace chrome
- device screens must show believable Material Design UI, never blank placeholders
- do not use any iOS-specific elements — no notch, no Dynamic Island, no rounded-square icons
```

### Watch 4-panel example

```text
Create one 1920x1920 App Store marketing composite for "PulseCheck" with a 2x2 grid (4 cells) and no visible seams.

Campaign:
- Goal: show at-a-glance health insights on the wrist
- Category: Health & Fitness
- Audience: health-conscious Apple Watch users who want quick vitals checks
- Core promise: your key health metrics in one glance
- Differentiator: complication-ready with smart alerts

Visual direction:
- Style family: bold, dark health UI
- Mood: vital, clinical-clean, trustworthy
- Palette: primary #000000, secondary #1C1C1E, accent #30D158, text #FFFFFF
- Motifs: heartbeat lines, ring charts, pulse waves

Typography:
- Headline only — no subtitles. 3-5 words max per cell.
- Extra large, extra bold, highest contrast. Tiny screens demand maximum legibility.
- Render all quoted text verbatim

Cells:
Cell 1 (top-left, hero):
- EXACT HEADLINE: "Your Vitals, Now"
- Role: hero — establish the core promise
- Layout: headline above a bold heart-rate ring
- Visual: large green ring chart at 82 BPM with a pulsing heartbeat line running through the center, black background
- No device frame

Cell 2 (top-right):
- EXACT HEADLINE: "Heart Rate Zones"
- Role: show real-time workout heart rate tracking
- Layout: headline above color-coded zone bars
- Visual: five horizontal bars in green/yellow/orange/red/purple representing Fat Burn through Peak zones, current zone highlighted with a glow, "142 BPM" large center text
- No device frame

Cell 3 (bottom-left):
- EXACT HEADLINE: "Sleep Score"
- Role: prove overnight health tracking value
- Layout: headline above a circular score
- Visual: a large "87" sleep score in white inside a blue-tinted ring, tiny labels for Deep/REM/Light segments around the ring, dark background
- No device frame

Cell 4 (bottom-right):
- EXACT HEADLINE: "Smart Alerts"
- Role: close with the safety/peace-of-mind angle
- Layout: headline above an alert notification
- Visual: a red alert card reading "Irregular Rhythm Detected" with a heart icon and a "View Details" button, dark background with a subtle red pulse glow
- No device frame

Constraints:
- each cell crops cleanly to 416x496
- keep text at least 30px from crop edges — small canvas demands tight but safe margins
- no borders, dividers, seams, gutters, or rounded crop corners
- no device bezels or watch frames — watch screenshots display without hardware chrome
- use large bold text and simple iconography — fine detail is invisible at watch scale
- no App Store badges, download buttons, or fake marketplace chrome
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
