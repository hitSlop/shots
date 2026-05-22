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

## Story Flow

Screenshots should form a cohesive Value -> Flow -> Trust narrative:

1. Value: lead with the outcome or problem solved.
2. Flow: show the solution in action with concrete product UI.
3. Trust: build confidence with proof, ratings, privacy, awards, or user count.

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
- 3-5 words per line
- concrete verbs and outcomes
- category-native words when useful
- no empty AI adjectives
- avoid `and` in headlines when possible
