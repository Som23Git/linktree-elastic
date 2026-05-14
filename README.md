# Personal Link Page

A minimal static link page you can deploy to Vercel in minutes. Comes with Elastic APM RUM wired in so you get real user monitoring -- page loads, web vitals, and link clicks -- flowing into your Elastic stack out of the box.

## What you get

- A clean link page served as a static site (zero server cost on Vercel's free tier)
- Link click tracking via a lightweight Vercel serverless function
- Browser RUM via the Elastic APM RUM agent -- page load times, LCP, FID, CLS, and unhandled JS errors sent to your Elastic deployment

## Deploy to Vercel

### 1. Fork and connect

Fork this repo, then go to [vercel.com](https://vercel.com), create a new project, and import your fork. Vercel will auto-detect the build settings from `vercel.json`.

### 2. Set environment variables

In your Vercel project go to **Settings -> Environment Variables** and add:

| Variable | Where to find it |
|---|---|
| `ELASTIC_APM_SERVER_URL` | Elastic Cloud -> your deployment -> APM server URL |
| `ELASTIC_APM_SECRET_TOKEN` | Elastic Cloud -> your deployment -> APM secret token |

These are used at **build time** (esbuild bakes the APM URL into the browser bundle) and at **runtime** (the serverless function uses them to send link click spans).

### 3. Enable RUM on your APM server

The browser RUM agent sends requests directly from the user's browser to your APM server. You need to enable CORS for this to work.

In your Elastic Cloud console go to your deployment -> **Edit** -> **APM & Fleet** -> user settings and add:

```yaml
apm-server.rum.enabled: true
apm-server.rum.allow_origins: ["*"]
```

Save and wait for the deployment to restart (~2 minutes).

### 4. Trigger a redeploy

Once the env vars are saved, go to **Vercel -> Deployments -> Redeploy** to trigger a fresh build that picks up the APM URL.

Your link page is now live and sending RUM data to Elastic.

## What you will see in Elastic

Open Kibana and go to **Observability -> APM -> Services**. You will see your service listed (the name is set in `assets/js/rum.src.js` under `serviceName`).

Under that service you get:

- **Transactions** -- one per page load, with full timing breakdown
- **Web vitals** -- LCP, FID, CLS, FCP, TTFB scored against Core Web Vitals thresholds
- **Errors** -- unhandled JS exceptions with stack traces
- **Dependencies** -- outbound fetch calls including the `/api/track` link click events

## Customising your links

Edit `regions/som.json`. Each link is one line:

```json
{ "title": "Button label", "url": "https://example.com" }
```

Links are grouped into sections. Add a new section by adding a new object to the `sections` array:

```json
{
  "title": "Section heading",
  "links": [
    { "title": "Link one", "url": "https://example.com" },
    { "title": "Link two", "url": "https://example.com" }
  ]
}
```

Commit the change and Vercel deploys automatically.

## Local development

```bash
npm install
node build.js
npx serve public -p 3000
```

The RUM agent will not initialise locally unless you export the env vars before building:

```bash
export ELASTIC_APM_SERVER_URL=https://your-deployment.apm.us-central1.gcp.elastic.cloud:443
export ELASTIC_APM_SECRET_TOKEN=your-secret-token
node build.js
```

## Project structure

```
regions/som.json      <- Your links. Edit this.
assets/js/rum.src.js  <- Browser RUM source (bundled by esbuild at build time)
api/track.js          <- Vercel serverless function for link click tracking
build.js              <- Builds static HTML into public/
scripts/              <- JSON validator
public/               <- Generated output, not committed
```

## Credits

Inspired by [LittleLink](https://github.com/sethcottle/littlelink) by Seth Cottle.
