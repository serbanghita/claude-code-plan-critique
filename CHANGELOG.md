# Changelog

All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2026-03-26

### Added
- Critique context: `/plan-execute` now reads `critique.md` as supplementary context during execution,
  carrying forward implementation hints, risk warnings, and alternative approaches from the critique phase.
- Plan readiness check: warns if a plan has not been critiqued before execution, with option to proceed.
- Git integration: per-step commits with rollback capability. After each step, offers to commit changes
  with a `plan-execute:` prefixed message. Tracks commit hashes in execution state for `git revert`.
- Post-step verification: runs `mcp__ide__getDiagnostics` on modified files after each step to catch
  new errors early. Advisory only, does not block execution.
- Error recovery options: on failure, presents four choices instead of just stopping: fix and retry,
  skip step, rollback via git revert, or stop execution.
- CLAUDE.md context: reads project standards at the start of execution and enforces compliance.
- Dependency graph: step presentation now shows inferred dependencies between steps and estimated
  file impact per step, with option for the user to reorder before execution begins.
- Supporting file classification: supporting files are classified by type and purpose, presented to
  the user for confirmation before execution.
- Step-level timing and file tracking in execution log.
- Context window warning for plans exceeding 10 steps.

### Changed
- Resume flow: on resume, verifies git state matches execution state. On restart, explicitly
  overwrites execution-log.md and notes the restart.
- Execution state format: added `skippedSteps`, `stepStartedAt`, `gitAvailable`, and `gitCommits`
  fields to `execution-state.json`.
- Execution log format: added `Duration` and `Files changed` fields per step, `Git commits` in
  summary. Failed steps now include verbatim error output.

## [1.1.0] - 2026-02-16

### Changed
- Session plan auto-selection: `/plan-critique`, `/plan-execute`, and `/plan-archive` now auto-select the current
  session plan instead of prompting every time. Plan selection prompt only appears when no session plan exists and
  multiple plans are available.

## [1.0.1] - 2026-02-09

### Changed
- Moved canonical version from VERSION file to `.claude-plugin/plugin.json`
- Hardcoded version in `/plan-create` banner to avoid reading files at runtime
- Removed unused `currentPlan` field from config file

### Removed
- VERSION file (replaced by plugin.json version field)

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
