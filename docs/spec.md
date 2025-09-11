# FIT Viewer – Specification (current version: see VERSION)

This document describes the current behavior, data flow, and UI of the FIT Viewer web app (current version: see VERSION).

- Privacy: 100% client-side; no uploads, no servers.
- Footprint: single `index.html` with JS, CSS, and Leaflet maps (OSM tiles).
- Hosting: static (GitHub Pages or any static host).

## 1. Data Ingestion and Parsing

### 1.1 FIT Parser
- Implements a minimal, self-contained FIT parser:
  - Validates header/signature (`.FIT`).
  - Supports definition and data messages (with/without developer fields).
  - Tracks compressed timestamp headers.
  - Captures messages:
    - Records (20)
    - Field descriptions (206)
    - File IDs (0)
    - Device info (23)
    - File creators (49)
- Decodes base types (int/uint/float/double)
- Developer data support:
  - Captures `devIndex:fieldNum` keys in `rec.dev`.
  - Parses field descriptions (206), extracting:
    - `name`, `units` (strings)
    - `scale` (field 6), `offset` (field 7) when present

### 1.2 Timestamps and Elapsed Time
- If `timestamp` is present, compute `_elapsed = timestamp - firstTimestamp`.
- If no timestamps exist, fall back to sample index (with a warning).

### 1.3 Device Metadata
- Captures manufacturer, product, serial, software from device info/file IDs.
- Normalize key Garmin product names (Forerunner/Edge/Venu/Vivoactive/Fenix/etc.).
- COROS quirk: manufacturer code 294 normalized to 167 (COROS).

## 2. Field Extraction and Units

### 2.1 Built-in Record Fields
- Heart Rate (field 3): `0–254` bpm → `hrRaw`.
- Cadence (field 4): device-specific scaling:
  - Garmin (1) or COROS (167): steps/stride → spm (×2) → `cadRaw`.
- Distance (field 5): centimeters → km (`/100000`) → `distRaw`.
- Speed / Pace:
  - Prefer field 6 (speed, m/s). Some devices scale by 1000 → decode: `>25 → /1000`.
  - Alternate enhanced speed: field 134.
  - Pace (min/km) = `(1000 / mps) / 60`, only if `mps > 0.05`.
  - Fallback: compute from distance deltas if needed.
  - Derived Speed (km/h) = `60 / pace`.
- Elevation:
  - Prefer `enhanced_altitude` (field 78), fallback `altitude` (field 2).
  - FIT spec scale/offset: meters = `raw/5 - 500`.
  - Discard impossible values (< -1000 or > 9000) as invalid.
  - Stored in `elevRaw`.

### 2.2 Developer Fields
- Automatic mapping for Core/Skin temps (developer data):
  - Heuristics based on `name`/`units` and value range.
  - For COROS, specific name patterns (`core_temperature`, `skin_temperature`) with sensible defaults for scale.
  - Final mapping can be overridden in the “Map Developer Fields” modal:
    - Select key, set `scale`, `offset` live.
- Heat Strain Index (HSI):
  - Detected by name: `"heat strain index"`, or strings containing `heat`+`strain` or `hsi`.
  - Values are clamped to [0, 5] for the line series; background zones use raw thresholds (see §4.3).

## 3. Smoothing, Trimming, and Selection

### 3.1 Zero‑phase Smoothing
- Slider maps 0–100 → 0–30 s (tau).
- Implemented as forward-backward EMA for zero‑phase characteristics.
- Applied to: core, skin, HR, HSI, distance, pace, speed, cadence, elevation.

### 3.2 Trim Early Noise
- Compute `cutoffX` = first `coreRaw` value > 0 (if exists).
- When Trim is ON, hide data before `cutoffX` across series and update the time-of-day window label accordingly.

### 3.3 Zoom and Selection
- Zoom mode toggles between inspection and selection.
- In Zoom mode: drag to select `[x1,x2]` (seconds). “Clear Zoom” resets.
- Selection affects:
  - Visible chart range
  - Stats (min/max/avg over view)
  - Map (selected segment highlighting; auto-fit to selection bounds)

## 4. Charting and Visualization

### 4.1 Axes and Series
- Dual Y axes:
  - Left: Core/Skin (°C)
  - Right: HR (bpm), HSI, Distance (km), Pace (min/km), Speed (km/h), Cadence (spm)
- X axis modes:
  - Elapsed time (default) – shows 00:00 at origin and increments
  - Time of day – origin labeled with start time, ticks as clock times
- Tick generation uses nice increments, labels formatted per mode.

### 4.2 Elevation Backdrop
- Auto-scaled to visible X-range.
- Drawn behind series with fill and a thin stroke (CSS vars `--elev-fill`, `--elev-stroke`).
- Uses smoothed elevation (`elevSeries`) respecting trim/selection.

### 4.3 HSI Background Bands
- Computed from HSI series samples; merged into horizontal bands spanning vertical plot space over time.
- Thresholds and colors (customizable via CSS variables):
  - HSI < 0.1: no shading
  - 0.1 ≤ HSI ≤ 1: green (`--hsi-bg-green`, default `#2e7d321a`)
  - 1 < HSI ≤ 3: orange (`--hsi-bg-orange`, `#ef6c001a`)
  - 3 < HSI ≤ 7: medium red (`--hsi-bg-red-med`, `#e539351a`)
  - HSI > 7: dark red (`--hsi-bg-red-dark`, `#b91c1c1a`)

### 4.4 Tooltips and Stats
- Crosshair shows `t = hh:mm:ss` (elapsed) or time-of-day.
- Per-series nearest value shown with formatting:
  - °C to 2 decimals, bpm integer, pace `m:ss /km`, distance km with 2 decimals, speed km/h with 2 decimals, cadence integer spm.
- Stats panel (visible range):
  - Max Core, Max Skin, Max HR, Window duration
  - Optional: Max HSI, Max Distance, Best/Avg Pace, Avg HR, Max Speed, Max Cadence – depending on toggles.

## 5. Map (Leaflet)

### 5.1 Initialization and Base Layers
- Lazy-initialized when coordinates are present.
- OpenStreetMap tile layer.
- Base route polyline (solid) with thicker default weight for visibility.

### 5.2 Markers, Selection, Sync
- Start marker: white fill, green outline, `weight:3`.
- Finish marker: white fill, red outline, `weight:3`.
- Moving marker: purple outline; follows chart crosshair; clicking on map moves chart crosshair to nearest route point.
- Selection layer: highlighted segment with auto-fit.

### 5.3 Route Coloring Modes
- Legend control offers selector: None / Heart Rate / Pace (fixed bands).
- HR mode:
  - Smooth gradient from blue → cyan → green → yellow → red based on HR value range.
- Pace mode (min/km) fixed-band coloring:
  - Before banding, clamp pace to `[3.0, 12.0]` min/km to avoid stop outliers dominating color scale.
  - Bands mapped to nominal values (e.g., 4.0, 4.5, 5.0, 6.0, 7.0) for consistent coloring.

### 5.4 Selection-aware Gradient
- If a time selection is active and HR mode is chosen, route coloring is restricted to the selected window for better contrast.

## 6. Controls and Toggles

- Series toggles: HR, Core, Skin, HSI, Distance, Pace, Speed, Cadence.
- Trim Early Noise.
- Time-of-day axis toggle.
- Zoom mode + Clear Zoom.
- Developer Field Mapping modal (Core/Skin): choose keys and set scale/offset.

## 7. Versioning, Releases, and Deployment

### 7.1 Versioning and CHANGELOG (GitHub Actions)
- Highest semver tag across the repo is treated as the "last version".
- Bump decision from commit range since last tag:
  - Major if any commit body contains `BREAKING CHANGE` OR the subject uses bang syntax `type(scope)!:`.
  - Minor if any subject starts with `feat:` (and not already major).
  - Patch otherwise (including `fix:` and all other types) — configurable.
- Workflow steps:
  - Compute next version, write `VERSION`.
  - Update both occurrences of `BETA v…` in `index.html`.
  - Prepend a generated section to `CHANGELOG.md` grouped by type.
  - Commit with `[skip ci]`, create annotated tag, and push.
  - Guard: if there are no commits since the last tag, skip the above steps.

### 7.2 Pages Deployment
- `.github/workflows/pages.yml` deploys the repo root as a static site to GitHub Pages on push to `main`.
- Concurrency cancels older builds; only the latest is deployed.

## 8. Extensibility and Configuration

### 8.1 CSS Variables
- Colors and opacity for series, HSI bands, selection box, elevation fill/stroke can be tuned via `:root` CSS vars.

### 8.2 Thresholds and Banding
- HSI thresholds are coded in `_drawHSIBackground() → zoneOf()`.
- Pace bands and clamp range `[3.0, 12.0]` are in `buildGradientValues('pace')`.

### 8.3 Performance Considerations
- Chart line rendering samples large series with `maxPts=5000` downsampling step.
- Elevation background draws at reduced density for performance.

## 9. Known Limitations / Notes
- Developer field auto-detection may not catch all vendor-specific schemas; the Mapping modal exists for overrides.
- Device/product name normalization is best-effort.
- Pace and speed depend on the reliability of speed/distance signals; clamping and smoothing mitigate outliers.
- Elevation accuracy follows device-provided data; decoded via standard FIT scale/offset.

## 10. API/Code Pointers (index.html)
- Parser class: `FitParser` (decoding FIT binary and routing messages).
- Smoothing: `smoothZeroPhase(points, tauSec)`.
- Series recomputation: `recalcSmoothing()`.
- Chart class: `LineChart` (rendering paths, backgrounds, tooltips).
- HSI background bands: `LineChart._drawHSIBackground()`.
- Elevation background: `LineChart._drawElevationBackground()`.
- Map functions: `ensureMap()`, `updateRouteOnMap()`, `addStartFinishMarkers()`, `updateSelectedSegmentOnMap()`, `updateMovingPositionOnMap()`.
- Gradient values: `buildGradientValues(kind)`.

---
If you need additional details (e.g., field number lookups, unit tables, or extended vendor mappings), we can expand this spec with appendices.
