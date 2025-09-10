# Conventional Commits Cheat Sheet

Format:

  type(optional-scope)!: short, imperative description

Body (optional): why/how context

Footer(s) (optional):
- BREAKING CHANGE: description
- Closes #123, Relates-to #456

Common types:
- feat: new feature (minor bump)
- fix: bug fix (patch bump)
- perf: performance improvements
- refactor: internal refactors
- docs: documentation
- style: formatting only
- test: tests
- build: build or deps
- ci: CI config
- chore: maintenance
- revert: revert a previous commit

Scopes: put the area in parentheses, e.g. (map), (chart), (parser), (release)

Breaking changes:
- Use ! after type/scope, e.g. feat(map)!: ...
- Or add a footer: BREAKING CHANGE: ...

Mapping to version bumps in this repo’s workflow:
- major: commit body contains "BREAKING CHANGE" OR subject matches bang syntax (type(scope)!:)
- minor: any subject starting with feat:
- patch: any subject starting with fix:

Examples:
- feat(map): add HR/pace gradient selector
- fix(map): white fill + red outline for finish marker
- perf(chart): reduce point density for smoother pan/zoom
- refactor(parser): extract altitude detection into helper
- docs(readme): add Quick Start for GitHub Pages
- chore(release): prepare v1.2.0
- feat(pace)!: change pace units to min/mi by default
  
  BREAKING CHANGE: default units changed; update user settings

Usage tips (GitHub Desktop):
- Put the first line in the Summary field.
- Add longer explanation or BREAKING CHANGE footer in the Description box.
- Push to main or merge a PR to main; the Action bumps version, updates VERSION and CHANGELOG, and tags.
