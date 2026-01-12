# claude-code-plan-critique

A Claude Code plugin for iterative plan review and execution. Write plans, get AI-powered critique, iterate until ready, then execute.

## Installation

### Option 1: Clone and Copy

```bash
git clone https://github.com/serbanghita/claude-code-plan-critique.git
cp -r claude-code-plan-critique/.claude /path/to/your/project/
```

### Option 2: Download and Extract

1. Download the [latest release](https://github.com/serbanghita/claude-code-plan-critique/releases)
2. Extract the `.claude` folder to your project root

### Option 3: Git Subtree (keeps updates)

```bash
git subtree add --prefix=.claude https://github.com/serbanghita/claude-code-plan-critique.git main --squash
```

### Post-Installation

Create a `CLAUDE.md` file in your project root with your project standards (required for critique to work):

```bash
cp claude-code-plan-critique/CLAUDE.md /path/to/your/project/
```

## Usage

### 1. Create Your Plan

Edit `.claude/plan/plan.md` with your execution plan. Describe what you want to achieve as clearly as possible.

You can also add supporting files to the `.claude/plan/` folder:
- SQL queries
- Text snippets
- Images or diagrams
- Reference documents
- Any other materials relevant to your plan

### 2. Critique Your Plan

Run in Claude Code:
```
/plan-critique
```

Claude will review your plan (including any supporting files) and write feedback to `.claude/plan/critique.md`. Review the critique, update your plan, and repeat until the status is `READY_TO_EXECUTE`.

### 3. Execute Your Plan

Run in Claude Code:
```
/plan-execute
```

Claude will parse your plan into steps, ask for confirmation, and execute each step. Progress is logged to `.claude/plan/execution-log.md`.

### 4. Archive Your Plan

Run in Claude Code:
```
/plan-archive
```

Claude will save your entire plan folder (including all supporting files) to `.claude/archived_plans/` for future reference.

## Commands

| Command | Description |
|---------|-------------|
| `/plan-critique` | Review and critique the current plan |
| `/plan-execute` | Execute the plan step by step |
| `/plan-archive` | Archive the plan for future reference |

## File Structure

```
.claude/
  plan/                        # Working directory for current plan
    plan.md                    # Your plan (edit this)
    critique.md                # Auto-generated critique
    execution-log.md           # Auto-generated execution log
    execution-state.json       # Auto-generated resume state
    [your-files]               # SQL, images, snippets, etc.
  archived_plans/              # Archived plan folders
    2026-01-12_14-30-00_feature-name/
      plan.md
      execution-log.md
      archive-info.md
      [archived supporting files]
  commands/
    plan-critique.md           # Critique command definition
    plan-execute.md            # Execute command definition
    plan-archive.md            # Archive command definition
```

## Critique Status Levels

| Status | Meaning |
|--------|---------|
| `NOT_READY` | Has Critical or Major issues - do not execute |
| `NEEDS_MINOR_CHANGES` | Only Minor issues remain - can execute with caution |
| `READY_TO_EXECUTE` | No blocking issues - safe to execute |

## Archive Naming Convention

Archived plans are stored as folders with the naming pattern:
```
YYYY-MM-DD_HH-MM-SS_[slug]/
```

Where `[slug]` is derived from the plan title (lowercase, hyphens, max 50 chars).

Example: `2026-01-12_14-30-00_add-user-authentication/`

## What Gets Archived

When you run `/plan-archive`:

**Included:**
- `plan.md` (the original plan)
- `execution-log.md` (if executed)
- All supporting files (SQL, images, etc.)
- `archive-info.md` (auto-generated metadata)

**Excluded:**
- `critique.md` (only keywords preserved in archive-info.md)
- `execution-state.json` (temporary state)

## Requirements

- [Claude Code](https://claude.ai/claude-code) CLI installed
- `CLAUDE.md` file in your project root with project standards

## License

MIT
