# Project Standards

This project provides slash commands for Claude Code that enable users to run four commands:
`/plan-create`, `/plan-critique`, `/plan-execute`, and `/plan-archive`.
These commands help create, review, execute, and archive plans written by the user with Claude Code.

## Architecture

Session tracking for parallel plans:
- Each Claude Code session tracks its current plan via `.planning/.sessions/[PID]` where PID is the Claude Code
  process ID obtained via `echo $PPID`.
- Commands read the session file to determine the current plan and show it as "(current session)" in selection.
- Stale session files (PIDs no longer running) are cleaned up opportunistically via `kill -0 [PID]` checks.
- The `.sessions/` directory is local state and should be gitignored.

## Code Style

- The commands markdown files from `commands` folder should have a word wrap of maximum 120 characters.
- The markdown text should be minimal, do not use bold or italic text unless absolutely necessary.
- The markdown text should be mostly plain text paragraphs, ordered lists, emphasys on code with backticks and some code blocks.
- The markdown text should include thematic breaks (`---`) to separate sections.
- The text from the commands markdown files should be written in plain English and easy to read from a text editor
  even if markdown support is not available.
- Never use emojis in the markdown text.

## Versioning and Releases

- The canonical version lives in `.claude-plugin/plugin.json` under the `version` field.
- Follow semver: MAJOR for breaking changes, MINOR for new features, PATCH for fixes.
- Every release must update four things:
  1. `version` field in `.claude-plugin/plugin.json`
  2. The hardcoded version in the banner inside `.claude/commands/plan-create.md`
  3. `CHANGELOG.md` with a new section describing the changes
  4. A git tag in the format `v[VERSION]` (e.g. `v1.0.0`)

## Documentation

- Short and succinct
- The README should have three major sections:
  - Install
    - Marketplace installation (recommended)
    - Manual installation via git clone
  - Usage
    - Pre-requisites
    - Commands overview
    - Workflow steps
  - Credits
