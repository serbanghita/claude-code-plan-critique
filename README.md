# claude-code-plan-critique

```
+-----------------+
| Plan & Critique |
+-----------------+
```

Slash commands for iterative plan review and execution in Claude Code.

**Version:** 1.0.0

## Install

Clone the repository and copy the commands to your project:

```bash
git clone https://github.com/serbanghita/claude-code-plan-critique.git && \
cp -r claude-code-plan-critique/.claude/commands .claude/ && \
rm -rf claude-code-plan-critique
```

**Note:** If Claude Code is already running, restart it to load the new commands.

## Usage

### Pre-requisites

- [Claude Code](https://claude.ai/claude-code) CLI installed
- A `CLAUDE.md` file in your project root with your project standards

### Commands

**`/plan-create`** - Create a new plan. On first run, asks where to store plans. Then asks for the plan name and
creates a folder with a template.

**`/plan-critique`** - Review your plan. Lists available plans, lets you select one, and generates a critique with
issues and suggestions.

**`/plan-execute`** - Execute your plan. Parses the plan into steps, asks for confirmation, and runs each step.
Supports resume on failure.

**`/plan-archive`** - Archive a completed plan. Moves it to the `archived/` subfolder and deletes the original.

### Workflow

1. Run `/plan-create` and provide a name
2. Edit the generated `plan.md` file
3. Run `/plan-critique` and iterate until satisfied
4. Run `/plan-execute` to implement
5. Run `/plan-archive` to clean up

## Credits

Created by [Serban Ghita](https://github.com/serbanghita) under MIT License
