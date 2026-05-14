# someshwaran.me links

A simple personal link page. Edit `regions/som.json` to update links, commit, and the page deploys automatically.

## Updating links

Edit `regions/som.json` -- each link is:

```json
{ "title": "Button label", "url": "https://example.com" }
```

## Local build

```bash
node build.js
npx serve public -p 3000
```

## Credits

Inspired by [LittleLink](https://github.com/sethcottle/littlelink) by Seth Cottle.
