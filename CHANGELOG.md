# Changelog

All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-02-08

### Added
- VERSION file for tracking releases
- Banner displayed at the start of `/plan-create` showing the current version
- CHANGELOG.md for documenting changes
- Versioning and release process documented in CLAUDE.md

### Changed
- Simplified the proposed ASCII banner to a plain-text box that fits 80-column terminals

## Pre-release history

Changes prior to the 1.0.0 release, grouped from the commit log.

### 2026-02-04

- Architectural changes mentioned in CLAUDE.md after plan execution (9dc4a7e)
- Improved README structure (a651ae3)

### 2026-01-14

- Critique command updated to the format of Claude Code official commands (a072042)
- All commands updated to follow the latest project standards (dda5f36)
- Fixed the install command (2314f68)
- Clarified plan naming rules (b4b66a2)
- Added line number references to the critique step (21880a9)

### 2026-01-13

- Added more clarity to critique and plan creation (5e6004d)
- Plan creation now forces the user to provide a custom plan name (72945be)
- Added "How it works" documentation (d99a75a)
- Improvements in clarity across commands (399e78b)
- Plan and Critique formatting refinements (5e6e102)

### 2026-01-12

- Initial release of claude-code-plan-critique (e42caad)
- Plugin packaging and publish setup (66eccdd)
- Installation process via git clone (0cda021, da14e61, bd0257d, a0e4e06)
