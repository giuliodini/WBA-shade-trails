# shadeTrails

**Sun-aware walking routes for urban environments.**

shadeTrails is an open-source proof of concept that explores how environmental intelligence can inform pedestrian navigation in cities. It retrieves walking routes between two points, scores each by shade coverage at a selected time of day, and ranks them shadiest-first.

---

## Scope & Limitations

shadeTrails is designed to explore the potential of integrating climate-related data into navigation systems, not to provide scientifically validated environmental simulations. Routing and shading results are approximate and depend on the underlying dataset, which may vary in quality and completeness by city.

This project is shared openly for experimentation and distributed as-is. If you encounter issues or would like to contribute, feel free to get in touch.

---

## How to use

1. Navigate to your area using the search box or by panning the map
2. Set the **date and time** of your trip using the bar at the bottom — shadows update live as you drag the slider
3. Click **A** then tap the map to set your origin; click **B** for your destination
4. Hit **Find and analyse routes** — routes are ranked by shade coverage

Each route is scored by the percentage of sampled points walked in shade and tagged with a role:

| Tag | Meaning |
|---|---|
| **Most shade** | Highest shade score |
| **Shortest** | Shortest total distance |
| **Best trade-off** | Best shade-to-distance ratio |
| **Most shade & shortest** | Both combined |

Every segment is colour-coded **teal** (shade) or **yellow** (sun). A label on the map shows distance, walk time and shade % for each route. Clicking an inactive label switches to that route.

---

## How the shade scoring works

### Route shade scoring

Each route is sampled into evenly spaced points. At each point, the ShadeMap SDK tests whether it falls in sun or shade at the simulated time. Points are coloured teal (shade) or yellow (sun) along the route. The shade score is the percentage of sampled points in shadow.

### Temporal correction

Each sample point is tagged with the walking time to reach it (at 5 km/h). The far end of a 30-minute walk is evaluated 30 minutes later than the start — correcting a systematic bias common in shade routing tools.

### Accuracy notes

- Shadow computation always runs at zoom 16 for maximum building resolution, regardless of the user's map zoom
- A warning appears for routes longer than 2 km straight-line distance, where accuracy may degrade
- Viewport coverage is limited to the central 70% of the canvas to avoid false sun readings from partially-loaded edge tiles
- A 400 ms settle buffer and 8 s timeout are applied after each viewport jump to ensure tiles are fully rasterised before sampling

---

## Run locally

```bash
python -m http.server 8080
```

Then open `http://localhost:8080`. The app must be served — not opened as `file://` — for CORS and the ShadeMap SDK to work.

> No Python? Download any 3.x version from [python.org](https://python.org).

---

## API keys required

shadeTrails uses the [ShadeMap SDK](https://shademap.app) for shadow computation. A free API token is available for educational and hobby projects running on localhost — visit [shademap.app](https://shademap.app) to request one.

Replace the placeholder in `config.example.js` and rename file in `config.js` with your key before running the app.

All other services used (OpenFreeMap, Valhalla, Nominatim, ipapi.co) require no API key.

---

## Tech stack

No install needed — all dependencies load from CDN.

| Library | Version | Purpose |
|---|---|---|
| [MapLibre GL JS](https://maplibre.org) | 5.2.0 | Map rendering |
| [Turf.js](https://turfjs.org) | latest | Route geometry and sampling |
| [mapbox-gl-shadow-simulator](https://github.com/ted-piotrowski/mapbox-gl-shadow-simulator) | latest | Shadow overlay + shade profiling |

| External service | Purpose |
|---|---|
| [OpenFreeMap](https://openfreemap.org) positron | Map tiles (no key required) |
| [Valhalla](https://valhalla1.openstreetmap.de) | Pedestrian routing, SARP diverse alternatives |
| [Nominatim](https://nominatim.openstreetmap.org) | Address geocoding |
| [ShadeMap](https://shademap.app) | 3D building shadow computation |
| [ipapi.co](https://ipapi.co) | IP-based geolocation (silent fallback to Rome) |

---

## File structure

```
index.html            ← entire app (HTML + CSS + JS, all inline)
config.example.js             ← ShadeMap API key — rename to config.js and replace placeholder with your key
assets/
  shadeTrails-icon.svg
  shadeTrails-splashscreen.svg
  shadeTrails-watermark.svg
README.md
```

---

## Curated by

[Giulio Dini](https://github.com/giuliodini)
