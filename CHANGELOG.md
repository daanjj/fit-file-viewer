## [0.5.12] - 2026-07-17

### Docs
- improve README formatting, styling, and documentation clarity

### Other
- e2fbad9 release: bump version to 0.6.0, remove BETA label, update release workflow, and refine README formatting
- b997b99 docs: improve README formatting, styling, and documentation clarity
- 373a113 style: update application title capitalization in index.html

## [0.6.0] - 2026-07-18

### Changed
- Remove BETA label from app title and header; version now shown as vX.Y.Z
- Update release workflow sed pattern to match new version string format
- Improve README formatting: add blank lines around headings, lists, and code blocks

## [0.5.11] - 2026-07-16

### Other
- 1ca7163 chore: remove client-specific branding from application title and header
- 682111f style: update title and header text for improved clarity

## [0.5.10] - 2026-07-16

### Other
- 87a77d8 style: add standard appearance property to range slider for cross-browser consistency
- 0085443 updated max value for HSI from 5 to 10

## [0.5.9] - 2025-11-14

### Other
- 2e25735 New LICENSE file (GNU v3.0)

## [0.5.8] - 2025-09-15

### Fixed
- consolidated into 1 row unless needed to break in 2 rows

### Other
- e68aaa5 Merge branch 'main' of github.com:daanjj/fit-file-viewer
- c6f430b fix(buttons): consolidated into 1 row unless needed to break in 2 rows

## [0.5.7] - 2025-09-15

### Other
- f0890fb fix(button sizes reduced)

## [0.5.6] - 2025-09-15

### Other
- 2fd5cdd support for power as well as visual improvements

## [0.5.5] - 2025-09-11

### Other
- 9e4a618 fix(android) for file picker

## [0.5.4] - 2025-09-11

### Fixed
- did not load

### Other
- 7a3ec54 fix(index): did not load

## [0.5.3] - 2025-09-11

### Other
- 68e46cb Merge branch 'main' of github.com:daanjj/fit-file-viewer
- b7d91dc fix(file picker on MacOS, iOS and Android)

## [0.5.2] - 2025-09-11

### Changed
- Mobile file picker UX:
  - iOS: use label-for="file" + offscreen input to ensure the native Files sheet opens reliably
  - Android: use File System Access API (when available) with FIT-only filters; fallback to hidden input
- Narrow accept types to FIT-only to avoid camera/photo providers

### Fixed
- iOS button unresponsive when programmatically clicking hidden input (use label/trusted gesture instead)

## [0.5.1] - 2025-09-11

### Other
- 6fcb129 chore(workflows)

## [0.5.0] - 2025-09-10

### Added
- show elevation profile

### Other
- 91d971a Merge branch 'main' of github.com:daanjj/fit-file-viewer
- 0ddc99d feat(index.html): show elevation profile

## [0.4.5] - 2025-09-10

## [0.0.5] - 2025-09-10

### Other
- a3ef036 chore: release numbering

## [0.0.4] - 2025-09-10

### Fixed
- initialize version baseline

### Other
- 95b88fc fix(release): initialize version baseline

## [0.0.3] - 2025-09-10

### Fixed
- initialize version baseline

### Other
- 23ab987 fix(release): initialize version baseline

## [0.0.2] - 2025-09-10

### Other
- c9961bb Update VERSION

## [0.0.1] - 2025-09-10

## [1.2.0] - 2025-09-11
### Added
- Leaflet route map with:
  - Start marker (white fill, green outline) and finish marker (white fill, red outline)
  - Moving position marker synced to chart crosshair and map clicks
  - Selection highlighting with auto-fit to selection
  - Route colorization by:
    - Heart Rate (smooth gradient)
    - Pace bands with outlier clamping [3.0, 12.0] min/km
- Elevation backdrop behind chart series (auto-scaled)
- Elevation series now smoothed with the same zero-phase smoothing as other series
- GitHub Pages workflow for automatic deployments
- Release workflow improvements: CHANGELOG generation, multiple "BETA v..." replacements in index.html, guard to skip when no new commits

### Changed
- HSI background bands thresholds updated:
  - <0.1 none, 0.1–1 green, >1–3 orange, >3–7 medium red, >7 dark red
- Default route line weight increased to match gradient thickness
- Finish marker redesigned for contrast; Start marker updated to white fill with green outline

### Fixed
- Proper Garmin elevation parsing using FIT scale (5) and offset (500)
- Pace color scaling no longer dominated by stops due to clamping

## [1.1.0] - 2025-09-05
### Added
- HSI-based background bands (green/orange/red)
- NOT YET WORKING: Elevation backdrop behind chart series
- NOT YET WORKING: Offline route canvas with tight bounds and start/end markers

### Changed
- docs/spec.md updated to v1.1.0
- README features updated
