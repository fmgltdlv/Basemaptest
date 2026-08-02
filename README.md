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
