# Personal Link Page -- Claude Guide

A static personal link page deployed on Vercel. Edit `regions/som.json` to update links.

## Structure

```
regions/som.json   <- Personal links. This is the only file that needs editing.
assets/            <- CSS, images, rum.src.js
api/track.js       <- Vercel serverless function for click tracking
build.js           <- Builds static site into public/
public/            <- Generated output. Do NOT edit directly.
```

## Region file schema

```json
{
  "region": "Som",
  "profile": {
    "name": "Your Name",
    "description": "One line bio."
  },
  "sections": [
    {
      "title": "Section heading",
      "links": [
        { "title": "Button label", "url": "https://example.com" }
      ]
    }
  ]
}
```

## Common tasks

**Add a link** -- edit `regions/som.json` and add to the relevant section's `links` array.

**Validate**
```bash
node scripts/validate-regions.js
```

**Build locally**
```bash
node build.js
npx serve public -p 3000
```
