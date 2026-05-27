# claude-code-plan-critique
> Plan -> Critique (N times) -> Execute -> Archive

Claude Code skills for iterative plan review and execution.  
Enables you to work with multiple user written plans while keeping control of the feedback-loop from the LLM.  
See [ghita.org/blog/claude-code-plan-critique](https://ghita.org/blog/claude-code-plan-critique/) for a better explanation of this project.

## How is this different from plan mode?

Claude Code has a built-in plan mode where Claude writes the plan and you approve or reject it.  
This plugin inverts that: you write the plan, Claude critiques it, and you decide which feedback
to accept. The critique loop can run as many times as you want.

It is slower by design. You read each critique, cherry-pick changes, and iterate until the plan
is yours — not Claude's interpretation of what you asked for. This is micro-management on purpose.

Plan mode is great when you trust Claude to drive. This plugin is for when you want to drive and
use Claude as a reviewer. The plans are persistent files you own, they can be archived for future
reference, and you can work on multiple plans in parallel across terminals.

## Install

### Marketplace installation (recommended)

Add the marketplace and install the plugin from within Claude Code:

```
/plugin marketplace add serbanghita/claude-code-plan-critique
/plugin install plan@serbanghita
```

Installed as a plugin, the skills are namespaced under the plugin name `plan`:
`/plan:create`, `/plan:critique`, `/plan:execute`, `/plan:archive`.

### Manual installation via git clone

Clone the repository and copy the skills to your project:

```bash
git clone https://github.com/serbanghita/claude-code-plan-critique.git && \
mkdir -p .claude/skills && \
cp -r claude-code-plan-critique/skills/* .claude/skills/ && \
rm -rf claude-code-plan-critique
```

Installed manually, the skills are invoked without a namespace:
`/create`, `/critique`, `/execute`, `/archive`. These bare names are generic, so prefer the
marketplace install if you run other skills with the same names.

If you upgraded from a previous manual install, delete the old `.claude/commands/plan-*.md` files.

If Claude Code is already running, restart it to load the new skills.

## How it works

```
/plan:create ──► edit 'plan.md' ──► /plan:critique ──► read 'critique.md', update 'plan.md'
                      ▲                                     │
                      └──────── iterate until satisfied ────┘
                                        │
                                        ▼
                              /plan:execute ──► /plan:archive
```

## Usage

### Pre-requisites

- [Claude Code](https://claude.ai/claude-code) CLI installed
- A `CLAUDE.md` file in your project root with your project standards

### Skills

Marketplace installs use the namespaced form below. Manual installs use the bare form
(`/create`, `/critique`, `/execute`, `/archive`).

1. `/plan:create` - Create a new plan in `.planning/[plan name]/plan.md`. Accepts the plan name as an argument.
2. `/plan:critique` - Claude Code reviews your plan. Generates a `critique.md` with issues and suggestions.
3. User decides which parts of critique are good for the plan and updates `plan.md`. Go back to 2.
4. `/plan:execute` - Execute your plan. Parses the plan into steps, asks for confirmation, and runs each step. Supports resume on failure.
5. `/plan:archive` - Archive a completed plan. Moves it to `.planning/archived/` and deletes the original.

### Parallel Plans

You can work on multiple plans simultaneously in separate terminals. Each Claude Code session tracks its own
current plan via session files in `.planning/.sessions/`.

Example workflow with two terminals:

```
Terminal 1                          Terminal 2
──────────────────────────────────  ──────────────────────────────────
/plan:create "Add Authentication"   /plan:create "Fix Database Bug"
edit plan.md                        edit plan.md
/plan:critique                      /plan:critique
...                                 ...
```

Each terminal remembers which plan it's working on. When you run `/plan:critique` or `/plan:execute`, your
session's plan is marked as "(current session)" in the selection list.

Add `.planning/.sessions/` to your `.gitignore` - these are local session files that should not be committed.

## Credits

Created by [Serban Ghita](https://github.com/serbanghita) under MIT License
