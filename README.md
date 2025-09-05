# FIT Viewer

Mobile-first, client-side FIT file viewer for heart rate, core/skin temperature, pace, distance, and more. Works offline after load; no uploads.

- Spec: see docs/spec.md (Version 1.0.0)
- Demo/Hosting: single static HTML (index.html). Host on GitHub Pages or serve locally.
- Privacy: 100% in-browser; your .fit files never leave your device.

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
- Device line: “<brand> <product> (sw version = X, serial= Y)”

## Quick Start (Developers)
1. Clone repo and open index.html in a modern browser, or:
2. Serve locally:
   ```bash
   python3 -m http.server 8000
   # open http://localhost:8000
