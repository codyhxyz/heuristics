# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.1] — 2026-04-21

### Changed
- `etl` frontmatter description rewritten as human-facing copy (was NLP routing triggers, useless with `disable-model-invocation: true`)
- Added ETL-vs-data-engineering callout explaining the stage rename
- Non-negotiable rule #3 now includes the *why* (analysis polluting raw breaks reproducibility)
- Scale signals table reordered to match pipeline order
- Stage definitions table includes a concrete filename example for `raw/`

## [0.1.0] — 2026-04-21

### Added
- `/heuristics:etl` — ETL research pipeline pattern: stage raw data separately from validation, processing, interpretation, and knowledge base. Raw is always required; other stages scale with scope.
