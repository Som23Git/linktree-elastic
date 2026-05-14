# Linkfolio

A minimal static link page you can deploy to Vercel in minutes. Comes with Elastic APM RUM wired in so you get real user monitoring, page loads, web vitals, and link clicks flowing into your Elastic stack out of the box.

## What you get

- A clean link page served as a static site (zero server cost on Vercel's free tier)
- Link click tracking via a lightweight Vercel serverless function
- Browser RUM via the Elastic APM RUM agent, page load times, LCP, FID, CLS, and unhandled JS errors sent to your Elastic deployment

## Deploy to Vercel

### 1. Fork and connect

Fork this repo, then go to [vercel.com](https://vercel.com), create a new project, and import your fork. Vercel will auto-detect the build settings from `vercel.json`.

### 2. Get your APM details from Elastic

If you already have Elastic Cloud with APM & Fleet Server running, your RUM details are ready to use with no extra config needed.

In Kibana go to **Observability -> User Experience**. At the top of the page you will find your APM Server URL and secret token. Copy both.

Alternatively you can find the same values under **Observability -> APM -> Add data -> RUM (JS)**.

### 3. Set environment variables

In your Vercel project go to **Settings -> Environment Variables** and add:

| Variable | Value |
|---|---|
| `ELASTIC_APM_SERVER_URL` | The APM server URL from the step above |
| `ELASTIC_APM_SECRET_TOKEN` | The secret token from the step above |

These are used at build time (esbuild bakes the APM URL into the browser bundle) and at runtime (the serverless function uses them to send link click spans).

### 4. Trigger a redeploy

Go to **Vercel -> Deployments -> Redeploy** to trigger a fresh build that picks up the env vars.

Your link page is now live and sending RUM data to Elastic.

## What you will see in Elastic

Open Kibana and go to **Observability -> APM -> Services**. You will see your service listed (the name is set in `assets/js/rum.src.js` under `serviceName`).

Under that service you get:

- **Transactions**: one per page load, with full timing breakdown
- **Web vitals**: LCP, FID, CLS, FCP, TTFB scored against Core Web Vitals thresholds
- **Errors**: unhandled JS exceptions with stack traces
- **Dependencies**: outbound fetch calls including the `/api/track` link click events

## Customising your links

Edit the relevant file under `regions/`. Each link is one line:

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
regions/              <- Your link files. Add or edit these.
assets/js/rum.src.js  <- Browser RUM source (bundled by esbuild at build time)
api/track.js          <- Vercel serverless function for link click tracking
build.js              <- Builds static HTML into public/
scripts/              <- JSON validator
public/               <- Generated output, not committed
```

## Credits

Inspired by [LittleLink](https://github.com/sethcottle/littlelink) by Seth Cottle.
