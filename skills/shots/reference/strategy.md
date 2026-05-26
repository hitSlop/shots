# Shots Strategy Brief And Benefits

Build this research pass once per app, then reuse it until the positioning changes. Store it in the app's `research` object with `apps.update_research` so Studio and future MCP sessions share the same source of truth.

When the pass produces App Store listing copy, store that separately with `apps.update_listing` for the target locale. Do not hide title/subtitle alternatives in `researchMarkdown`.

## Source Priority

1. user-provided positioning, audience, and brand constraints
2. saved app context from `apps.get` or imported App Store listing screenshots from `apps.import`
3. local repo README/docs/source files
4. competitor listings and review language

## Research Pass

### Product

Read, in order:

1. README and docs
2. onboarding, tutorial, paywall, and landing copy
3. navigation and root screens
4. models, schemas, APIs
5. existing screenshots or public listing screenshots

Capture:

- what the app does
- who it is for
- which moments, pains, and outcomes matter most

### App Experience

Deep-dive into how the app actually looks, works, and feels. This section becomes the primary content for `researchMarkdown` and feeds directly into screenshot prompt accuracy.

#### Sources

Research material priority depends on app status:

**Published apps:**
1. App Store screenshots (imported via `apps.import`) — analyze each screen in detail
2. App Store description and release notes
3. Source code (screens, components, navigation)

**Unpublished apps:**
1. Screen/view components (SwiftUI views, React components, Activity/Fragment layouts)
2. Navigation files (router, tab bar, nav stack definitions)
3. Design tokens and theme files (colors, typography, spacing constants)
4. Preview fixtures and storybook stories
5. README and docs

#### Branding & Aesthetic

Capture the full visual identity beyond the style family:

- **Color system** — hex values for: primary, secondary, accent, background, surface, text (light and dark variants if applicable)
- **Typography** — font families, weights used, size hierarchy (heading, subheading, body, caption)
- **Shape language** — corner radii (e.g. 12px cards, 8px buttons), card styles, shadow values
- **Iconography style** — outlined, filled, duotone, custom illustrations, SF Symbols usage
- **Dark/light mode** — which is the primary presentation; differences between modes
- **Brand personality through UI** — minimal? information-dense? playful? clinical? warm?
- **Distinctive visual signatures** — what makes this app recognizable at a glance (e.g. gradient headers, bottom sheet navigation, floating action button style)

#### Core Problem & Solution

- **The problem in the user's words** — how the target user describes their frustration before finding this app
- **How the app solves it** — the mechanism, not the feature list (e.g. "automated categorization" not "AI-powered tags")
- **The "aha moment"** — when the user first feels the value (e.g. "seeing their first weekly spending breakdown")
- **What success looks like** — the outcome with regular use (e.g. "never overdrafts, always knows where money goes")

#### Critical User Flows

Document 3-5 key flows that represent the app's core value:

```
Flow: [Name]
Trigger: [What starts this flow — e.g. "user taps + button on home screen"]
Steps:
  1. [Screen name] — [what the user sees and does]
  2. [Screen name] — [what the user sees and does]
  3. [Screen name] — [what the user sees and does]
Marketing highlight: Step [N] — [why this step shows the most compelling UI]
```

Mark which steps show the most compelling UI for marketing screenshots.

#### Critical Screens

For each screen that could appear in a marketing screenshot (aim for 4-8 screens):

```
Screen: [Name]
Layout: [top-to-bottom structure — e.g. "nav bar → search field → filter chips → scrollable card grid → tab bar"]
Key UI elements: [specific components — e.g. "circular progress ring at 73%, streak counter showing '12 days', category pills"]
Data shown: [representative content — e.g. "3 expense entries totaling $47.50, 'Groceries' category selected"]
Interactive elements: [buttons, toggles, sliders and their current states — e.g. "green toggle ON for 'Auto-categorize', disabled 'Export' button"]
Emotional payload: [what feeling the screen evokes — e.g. "accomplishment, control, clarity"]
Marketing value: [what benefit this screen proves — e.g. "proves the app surfaces spending patterns effortlessly"]
```

These descriptions feed directly into `device.screen` fields during prompt building. The more specific and visual the description, the more accurate the generated screenshot.

#### User Achievement

- **Identity shift** — "I am someone who [has control over my finances / meditates daily / ships on time]"
- **Capability gained** — "I can now [see where my money goes without manual tracking]"
- **Pain removed** — "I no longer [worry about surprise charges / forget important tasks]"
- **Social proof moment** — "Others notice that I [always know my numbers / seem less stressed]"

### Visual System

Infer theme from brand colors, design tokens, assets, app icon, screenshots, and user constraints. Pick one style family:

| Family | Best fit |
| --- | --- |
| `clean-premium` | finance, productivity, premium utility |
| `playful-collage` | social, creator, youth |
| `glossy-y2k` | Gen Z, entertainment, bold social |
| `editorial-luxury` | fashion, lifestyle, luxury |
| `utility-bold` | crowded categories that need punch |
| `calm-warm` | wellness, journaling, family |
| `sport-kinetic` | fitness, live events, action |

### Competitors

Identify 3-5 direct competitors. For each, capture:

- `name`
- `whyTheyCompete`
- `visualPattern`
- `copyPattern`
- `gapToExploit`

### Language Bank

Pull repeated phrases from listing copy, reviews, competitor screenshots, and repo copy. Distill:

- `voice.tone`
- `voice.marketWords`
- `voice.reviewPhrases`
- `voice.tabooWords`

## Keyword Research Process

Run this process when the user asks for keyword research, ASO optimization, or metadata work. Without an external keyword API, volume and difficulty are LLM estimates on a 1-100 scale — label them as estimates.

### 1. Seed Expansion

Generate 30-50 keyword candidates from:

- **App description and features** — core functionality terms
- **Competitor titles, subtitles, and descriptions** — terms they rank for
- **Problem-based terms** — how users describe the pain, not the solution (e.g. "can't sleep" not "sleep tracker")
- **Synonyms and related terms** — alternate phrasings for the same intent
- **Category terms** — words users browse by in the App Store

### 2. Evaluation

For each candidate keyword, estimate:

| Metric | Scale | What it measures |
| --- | --- | --- |
| Volume | 1-100 | Relative search frequency |
| Difficulty | 1-100 | How hard it is to rank (competitor density, brand dominance) |
| Relevance | 1-100 | How well it matches the app's core value proposition |
| Intent | download / informational | Whether the searcher is ready to install |

Prefer download-intent keywords. Informational keywords belong in long-tail or description copy, not title/subtitle.

### 3. Opportunity Scoring

Compute a composite score for each keyword:

```
Score = (Volume × 0.4) + ((100 - Difficulty) × 0.3) + (Relevance × 0.3)
```

Store the computed score in the `score` field of each opportunity object.

### 4. Grouping

Sort scored keywords into the existing schema buckets:

- **`primaryKeywords`** (3-5): highest opportunity score, must appear in title or subtitle
- **`secondaryKeywords`** (5-10): target in subtitle and keyword field
- **`longTailKeywords`** (10-20): fill keyword field, easier to rank for
- **`opportunities[]`**: all evaluated candidates with volume, difficulty, relevance, priority, score, and notes

### 5. Keyword Field Construction

Pack `keywordField` to use all 100 characters (iOS):

- Comma-separated, **no spaces after commas**
- **Never repeat** words already in title or subtitle
- **Singular forms only** — Apple indexes both singular and plural
- **Exclude**: app name, category name, "app", "free"
- Order by opportunity score descending
- Verify the final string is ≤ 100 characters

### 6. Competitor Keyword Gaps

When competitor apps are known (from `competitorAnalysis.competitors`), produce:

1. **Gap keywords** — terms competitors use that the app does not → opportunities to fill
2. **Advantage keywords** — terms the app uses that competitors do not → protect these positions
3. **Outranked keywords** — terms where competitors have stronger placement → improvement targets

Store gap findings in `competitorAnalysis.competitors[].keywordGaps` and surface the top opportunities in `keywordStrategy.opportunities[].notes`.

## Metadata Rules

Apply these constraints when generating App Store listing copy:

### Title Formulas

- `[Brand] - [Primary Keyword]` or `[Brand]: [Benefit Phrase]`
- Lead with brand if the app is well-known; lead with keyword if not
- iOS and Android: 30 character limit

### Subtitle

- 30 character limit (iOS); 80 character short description (Android)
- Use secondary keywords not already in the title
- Focus on a benefit or use case, not a feature list

### Keyword Coverage Matrix

Output a matrix showing where each primary and secondary keyword appears:

```
Keyword          | Title | Subtitle | Keyword Field
─────────────────|───────|──────────|──────────────
budget tracker   |   ✓   |          |
expense log      |       |    ✓     |
money manager    |       |          |      ✓
```

Every primary keyword must appear in at least one field. No keyword should appear in more than one field — Apple indexes title, subtitle, and keyword field separately.

### Variant Generation

Generate 3 title options and 3 subtitle options, each with:

- The copy text
- Character count
- Which primary/secondary keywords it covers

### Localization

When generating listing copy for non-primary locales: **do NOT translate English keywords.** Research what local users actually search for. Each locale needs independent keyword research because users search differently:

- English "budget tracker" → German "Haushaltsbuch" → Japanese "家計簿"

## Research And Listing Schema

```json
{
  "research": {
    "appSummary": "",
    "category": "",
    "targetAudience": "",
    "marketMaturity": "",
    "competitors": [
      {
        "name": "",
        "notes": ""
      }
    ],
    "positioning": {
      "primaryPromise": "",
      "differentiators": [],
      "proofPoints": []
    },
    "voice": {
      "tone": "",
      "wordsToUse": [],
      "wordsToAvoid": []
    },
    "visualTheme": {
      "style": "",
      "colors": [],
      "notes": ""
    },
    "benefits": [],
    "researchMarkdown": "",
    "sources": [],
    "asoAudit": {},
    "keywordStrategy": {},
    "competitorAnalysis": {},
    "iconAudit": {},
    "localizationPlan": {}
  },
  "localeListing": {
    "locale": "en-US",
    "title": "",
    "subtitle": "",
    "description": "",
    "keywords": [],
    "titleSuggestions": [],
    "subtitleSuggestions": []
  },
  "asoResearch": {
    "asoAudit": {},
    "keywordStrategy": {},
    "competitorAnalysis": {},
    "iconAudit": {},
    "localizationPlan": {}
  }
}
```

**Note on `researchMarkdown`:** The `researchMarkdown` field should contain the structured App Experience section as its primary content — branding & aesthetic details, core problem & solution, critical user flows with step-by-step screen descriptions, critical screen layouts with specific UI elements and data, and user achievement framing. This gives the generation pipeline enough detail to produce accurate device screen descriptions without inventing UI.

## Story Flow

Screenshots should form a cohesive Value -> Flow -> Trust narrative:

1. Value: lead with the outcome or problem solved.
2. Flow: show the solution in action with concrete product UI.
3. Trust: build confidence with proof, ratings, privacy, awards, or user count.

## Screenshot Title Strategy

Screenshot headlines are conversion hooks, not labels. Write the smallest emotional promise that makes a browsing user stop, understand the value, and inspect the UI. The best headline usually names the benefit, relief, identity shift, curiosity gap, or before/after transformation the user wants.

Use these patterns:

- Outcome: what the user gets now, e.g. "Sleep Better Tonight"
- Pain relief: what friction disappears, e.g. "Never Miss a Receipt"
- Identity/permission: who the user gets to feel like, e.g. "Feel Ready for Anything"
- Personal relevance: why this is about them, e.g. "See What Fits You"
- Transformation: the before/after state, e.g. "From Chaos to Clarity"
- Proof: only when supplied by app context, e.g. "Trusted by 50K Teams"

Before -> after examples:

| Weak title | Better screenshot title |
| --- | --- |
| AI Meal Planner | Know Dinner by 5 |
| Track Your Sleep | Wake Up Actually Rested |
| Budgeting Dashboard | Stop Wondering Where It Went |
| Color Analysis Scan | Find Your Best Colors |
| Job Auto Apply | Apply Before They Close |
| Habit Reminders | Make Progress Feel Automatic |
| Calendar Sync | See Your Day Clearly |
| Photo Filters | Make Every Shot Pop |
| Workout Library | Train Without Overthinking |
| Invoice Generator | Get Paid Without Chasing |

Quality bar:

- 3-6 words whenever possible.
- Meaningful at thumbnail size without reading the subtitle.
- Concrete enough to visualize; avoid vague SaaS or AI language.
- Uses customer language, not internal product terminology.
- Pairs with real UI so the screenshot visually proves the claim.
- Subtitle clarifies the feature, but the headline carries the hook.

Avoid:

- Generic feature labels: "AI Dashboard", "Smart Tracking", "Advanced Analytics".
- Empty hype: "Ultimate", "Revolutionary", "Game-Changing", "Best".
- Unsupported claims: #1, top rated, millions of users, awards, testimonials, revenue, or ranking unless supplied by app context.
- Shame, insecurity exploitation, gender stereotypes, or making users feel broken.
- CTA headlines like "Download Now", especially for Google Play.

Ethical emotional framing:

- Recognize the user's problem without insulting them.
- Make the desired action feel clear, easy, and attainable.
- Create curiosity with personally relevant results or transformations.
- Prefer recognition over persuasion: the title should feel like "this app gets me."

## Benefits

Draft 6-8 benefits from the brief, not from a feature list. Each benefit needs:

```json
{
  "headline": "",
  "subtitle": "",
  "approach": "moment",
  "panelType": "ProductTour",
  "feature": "",
  "arcPosition": "core",
  "showDevice": true,
  "textPosition": "top",
  "breakoutElements": ""
}
```

Rules:

- one promise per headline
- 3-6 words per headline
- concrete verbs and outcomes
- category-native words when useful
- no empty AI adjectives
- avoid `and` in headlines when possible
- headline must be a user benefit or emotional hook, not the feature name
- use the before -> after examples above as rewrite patterns
