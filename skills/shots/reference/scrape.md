# Shots App Store Lookup

Fetch App Store metadata and public screenshot URLs to seed strategy. This flow does not generate images.

## Steps

### 1. Resolve The App

Accept an App Store URL or app id. If neither is available, ask for one.

### 2. Lookup Listing

Call `shots` with `appstore.lookup` using the app id or URL, the user's country code if specified, and the target locale if known.

### 3. Summarize

Report:

- app name
- developer
- version
- rating and review count
- genres
- screenshot source and device breakdown

### 4. Save Listing Copy

When durable app context exists, draft the App Store listing object and call `apps.update_listing` with the target locale:

- keep the imported title unless a stronger title is obvious
- add one selected subtitle
- preserve or lightly tighten the public description
- generate 8-20 keywords from the category, feature set, and audience
- generate 3-6 title suggestions and 3-6 subtitle suggestions
- generate 1-3 description alternatives when the user asks for metadata work

### 5. Offer Generation

Ask whether the user wants to continue into [create.md](create.md). If yes, use the lookup result as the App Store source for the creation strategy.
