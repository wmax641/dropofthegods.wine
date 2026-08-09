# Architecture

## Purpose

`dropofthegods.wine` is a small static website for cocktail and bar tools. It is designed
to run directly from static hosting, with no build step, package manager, server runtime,
database, or generated asset pipeline.

## Runtime Model

- Static HTML files are the application entry points
- CSS is embedded in each page rather than shared through a global stylesheet
- JavaScript is embedded in each page and scoped to that page
- Data is stored as local JSON files under `recipes/`
- Images are committed directly under `images/` and `recipes/images/`
- `CNAME` configures the custom GitHub Pages domain

Keep changes compatible with static hosting. Avoid introducing build tooling or backend
dependencies unless the task explicitly requires it.

## Routes

| Path | File | Role |
| --- | --- | --- |
| `/` | `index.html` | Landing page with rotating bar photography and navigation |
| `/recipes/cocktails.html` | `recipes/cocktails.html` | Cocktail recipe browser |
| `/brix/brix.html` | `brix/brix.html` | Syrup and fruit Brix adjustment calculator |

## Landing Page

`index.html` presents the site brand, links to the two tools, and a rotating full-screen
background gallery of bars.

Key implementation details:

- `backgroundImages` is an array of `[imagePath, venueName, location]`
- Two fixed `.bg-layer` elements crossfade backgrounds by toggling opacity
- The caption area displays the active venue and location
- Dot buttons let users select a background manually
- The slideshow interval is controlled by `slideIntervalMs`

When adding or removing landing page photos, update both `images/` and the
`backgroundImages` array.

## Cocktail Recipes

`recipes/cocktails.html` loads recipe data from:

- `recipes/classics.json`
- `recipes/dropofthegods.json`
- `recipes/riffs.json`

The page builds filters from the loaded recipes and renders recipe cards entirely on the
client. It uses native browser APIs only.

Expected recipe fields:

```json
{
  "name": "Recipe name",
  "style": "Sour",
  "base": "Gin",
  "category": "Classics",
  "img": "images/example.jpg",
  "ingredients": [
    { "amount": 45, "unit": "mL", "type": "Optional descriptor", "ingredient": "gin" }
  ],
  "method": ["shake", "double strain"],
  "notes": "Optional note"
}
```

`ingredients` may contain structured objects or plain strings. `method` entries may contain
simple HTML markup such as `<b>` and `<br>`, so treat the JSON files as trusted site content.

When adding recipes:

- Put recipe images under `recipes/images/`
- Use paths relative to `recipes/cocktails.html`, for example `images/daiquiri.jpg`
- Keep category values aligned with filter behavior: `Classics`, `Riffs`, or
  `Drop of the Gods`
- Prefer consistent units such as `mL`, `g`, and descriptive plain strings for complex
  preparations

## Brix Calculator

`brix/brix.html` is a standalone AngularJS 1.8.2 page loaded from the Google CDN. It
contains all calculator UI, data, and formulas inline.

The `BrixController` manages:

- Known fruit Brix ranges
- Known syrup Brix values
- Manual initial and desired Brix inputs
- Total sugar/water adjustment calculations
- Validation and error display

Keep calculator behavior page-local unless a broader shared architecture is deliberately
introduced.

## Directory Map

```text
.
├── CNAME
├── README.md
├── architecture.md
├── index.html
├── brix/
│   └── brix.html
├── images/
│   └── bar*.jpg
└── recipes/
    ├── cocktails.html
    ├── classics.json
    ├── dropofthegods.json
    ├── riffs.json
    └── images/
        └── *.jpg
```

## Agent Working Notes

- Read this file before making structural changes
- Preserve the static-site model by default
- Keep page-specific changes in the owning HTML file
- Keep recipe data changes in the JSON files rather than hard-coding recipe cards
- Use local image paths that work from the page being edited
- Be careful with viewport height and `overflow: hidden` on mobile pages
- Keep comments brief and useful; do not comment trivial lines
- Prefer maximum line width of 100 characters where practical

