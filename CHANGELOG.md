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
