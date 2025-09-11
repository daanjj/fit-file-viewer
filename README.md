# FIT Viewer

[![Deploy to GitHub Pages](https://img.shields.io/badge/Pages-Live-blue?logo=github)](https://daanjj.github.io/fit-file-viewer/)

Mobile-first, client-side FIT file viewer for heart rate, core/skin temperature, pace, distance, and more. Works offline after load; no uploads.

- Spec: see docs/spec.md (Version: see VERSION)
- Demo/Hosting: single static HTML (index.html). Host on GitHub Pages or serve locally.
- Privacy: 100% in-browser; your .fit files never leave your device.
- Versioning: Automatic version bump via GitHub Actions and tags (see CONVENTIONAL_COMMITS.md)

## Features (current version: see VERSION)
- Local .FIT parsing; developer field mapping (core/skin)
- Dual-axis chart with zero-phase smoothing (0–30 s)
- HSI background bands:
- <1 green, 1–<2 orange, ≥2 red
- Elevation backdrop behind the series (auto-scaled)
- Route canvas (offline), tightly fit to track; start/end markers
- Touch-friendly:
- Zoom OFF: finger slide to inspect with crosshair
- Zoom ON: drag-select time window; Clear Zoom to reset
- Pace from enhanced speed (m/s) → min/km; distance default cm→km
- Maxima reflect visible range; device metadata displayed

## Features (v1.0.0)
- Local .FIT parsing (records, developer fields, device metadata)
- Core/Skin temps on primary Y-axis; HR/pace/distance/cadence/HSI on secondary
- Touch-friendly:
  - Zoom mode OFF (default): slide finger to inspect values with a crosshair
  - Zoom mode ON: drag to select a time range; “Clear Zoom” to reset
- Zero-phase smoothing (0–30 s)
- Pace from enhanced speed (m/s) → min/km; fallback to speed or distance deltas
- Distance shown in km; default assumption distance is in centimeters (cm→km)
- Mapping modal for developer fields (core/skin), with scale factors
- Device line: “<brand> <product> (sw version = X, serial = Y)”

## Quick Start (Developers)

### Deployment (GitHub Pages)
- The site deploys automatically via GitHub Actions (see .github/workflows/pages.yml)
- Live URL: https://daanjj.github.io/fit-file-viewer/
- If you change the site folder, update pages.yml path: .

### Release & Versioning
- Versioning is automated via .github/workflows/release.yml and tags.
- Conventional commits mapping (see CONVENTIONAL_COMMITS.md):
  - major: BREAKING CHANGE in body or bang syntax (feat(scope)!: ...)
  - minor: feat:
  - patch: fix: (default if neither major/minor)
- Starting at a specific version (e.g., 0.4.3):
  1. Create tag v0.4.2 on the baseline commit (GitHub Releases page is fine)
  2. Make a small patch commit to main (e.g., fix(release): initialize version baseline)
  3. Action bumps to v0.4.3, updates VERSION, CHANGELOG.md, and both BETA v... strings in index.html

### Show Version in the App
- The release workflow updates both occurrences of BETA v... in index.html.
- Optional dynamic approach: display Version: vX.Y.Z by reading VERSION at runtime.
  Add this placeholder element near the header:
  ```html
  <div id="appVersion" class="device-line" aria-label="App version"></div>
  ```
  And this script snippet inside the existing <script> in index.html:
  ```html
  <script>
  (async () => {
    try {
      const res = await fetch('VERSION', { cache: 'no-store' });
      if (res.ok) {
        const ver = (await res.text()).trim();
        const el = document.getElementById('appVersion');
        if (el) el.textContent = 'Version: v' + ver;
      }
    } catch (_) {}
  })();
  </script>
  ```
1. Clone repo and open index.html in a modern browser, or:
2. Serve locally:
   ```bash
   python3 -m http.server 8000
   # open http://localhost:8000
   ```

3. Conventional commits + release automation
   - See CONVENTIONAL_COMMITS.md for a quick cheat sheet
   - Optional: set the commit template locally so your commit editor is pre-filled:
     ```bash
     git config commit.template .github/COMMIT_TEMPLATE.txt
     ```
   - On push to main, the "release" GitHub Action bumps the version, prepends to CHANGELOG.md, updates VERSION and index.html version strings, tags, and pushes.
