# Project Standards

This project provides slash commands for Claude Code that enable users to run four commands:
`/plan-create`, `/plan-critique`, `/plan-execute`, and `/plan-archive`.
These commands help create, review, execute, and archive plans written by the user with Claude Code.

## Code Style

- The commands markdown files from `commands` folder should have a word wrap of maximum 120 characters.
- The markdown text should be minimal, do not use bold or italic text unless absolutely necessary.
- The markdown text should be mostly plain text paragraphs, ordered lists, emphasys on code with backticks and some code blocks.
- The markdown text should include thematic breaks (`---`) to separate sections.
- The text from the commands markdown files should be written in plain English and easy to read from a text editor
  even if markdown support is not available.
- Never use emojis in the markdown text.

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
