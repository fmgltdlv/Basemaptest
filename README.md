# Basemaptest

Interactive test viewer for the Clark County, Nevada aerial imagery ArcGIS MapServer.

## Service

- **URL:** https://maps.clarkcountynv.gov/arcgis_images/rest/services/CACHED/mostcurrentflight/MapServer
- **Type:** Cached tile map service (`singleFusedMapCache: true`)
- **Imagery:** Spring 2020 orthophoto mosaic
- **CRS:** EPSG:3421 (NAD83 / Nevada State Plane East, US survey feet)
- **Tile format:** 512×512 JPEG
- **Zoom levels:** 0–9 (scales 1:1,000,000 down to 1:500)

### Layers

| ID | Name      | Type           |
|----|-----------|----------------|
| 0  | Spring2020| Mosaic Layer   |
| 1  | Boundary  | Feature (poly) |
| 2  | Footprint | Feature (poly) |
| 3  | Image     | Raster         |

### Tile URL pattern

```
{MapServer}/tile/{level}/{row}/{col}
```

Example (Las Vegas area, level 5):

```
https://maps.clarkcountynv.gov/arcgis_images/rest/services/CACHED/mostcurrentflight/MapServer/tile/5/4446/4196
```

### Dynamic export

```
{MapServer}/export?bbox={xmin},{ymin},{xmax},{ymax}&bboxSR=3421&imageSR=3421&size=400,400&f=image
```

## Run locally

```bash
npm install
npm start
```

Open http://localhost:3000

You can also open `index.html` directly in a browser; a local static server is recommended so the ArcGIS API loads reliably.

## Implementation notes

This service uses Nevada State Plane East (feet), not Web Mercator. The viewer uses the ArcGIS Maps SDK for JavaScript, which reads the service tile scheme and spatial reference automatically.

## Deploy to Cloudflare

**Use Cloudflare Pages, not Workers.**

This project is a static `index.html` that loads the ArcGIS SDK and imagery tiles from third-party hosts. There is no server-side logic, API routes, or build step. [Cloudflare Pages](https://developers.cloudflare.com/pages/) is the right fit: it serves static files, supports Git-based deploys, preview URLs on pull requests, and custom domains.

Choose **Workers** only if you later add edge logic (for example, proxying tile requests, injecting auth headers, or rate limiting). That is not needed for the current viewer.

### Option A — Pages via Git (recommended)

1. Open [Workers & Pages](https://dash.cloudflare.com/?to=/:account/workers-and-pages) in the Cloudflare dashboard.
2. Select **Create** → **Pages** → **Connect to Git**.
3. Choose the `fmgltdlv/Basemaptest` repository.
4. Use these build settings:

   | Setting | Value |
   |---------|-------|
   | Framework preset | None |
   | Build command | *(leave empty)* |
   | Build output directory | `.` |

5. Select **Save and Deploy**.

Cloudflare will deploy `index.html` from the repo root on each push to the production branch. Pull request previews are created automatically if enabled in the project settings.

### Option B — Pages via Wrangler CLI

Install Wrangler and log in once:

```bash
npm install -g wrangler
wrangler login
```

Deploy the site:

```bash
npm run deploy
```

Or directly:

```bash
npx wrangler pages deploy . --project-name=basemaptest
```

`node_modules` is gitignored and is not uploaded.

### Option C — Workers static assets (optional)

If you prefer a Worker hostname (`*.workers.dev`) or plan to add edge code later:

```bash
npx wrangler deploy .
```

Wrangler will prompt to create a static-assets configuration on first run. No `wrangler.jsonc` is required upfront.

For this repo today, **Option A or B (Pages)** is simpler and sufficient.

### After deploy

- Set a custom domain under **Pages** → your project → **Custom domains** if needed.
- The map loads scripts from `js.arcgis.com` and tiles from `maps.clarkcountynv.gov`; no extra Cloudflare configuration is required unless you add a restrictive Content Security Policy.
