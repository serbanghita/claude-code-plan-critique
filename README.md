# claude-code-plan-critique
> Plan -> Critique (N times) -> Execute -> Archive

Claude Code commands for iterative plan review and execution.  
Enables you to work with multiple user written plans while keeping control of the feedback-loop from the LLM.  
See [ghita.org/blog/claude-code-plan-critique](https://ghita.org/blog/claude-code-plan-critique/) for a better explanation of this project.

## Install

Clone the repository and copy the commands to your project:

```bash
git clone https://github.com/serbanghita/claude-code-plan-critique.git && \
mkdir -p .claude/commands && \
cp -r claude-code-plan-critique/.claude/commands/* .claude/commands/ && \
rm -rf claude-code-plan-critique
```

**Note:** If Claude Code is already running, restart it to load the new commands.

## How it works

```
/plan-create ──► edit 'plan.md' ──► /plan-critique ──► read 'critique.md', update 'plan.md'
                      ▲                                     │
                      └──────── iterate until satisfied ────┘
                                        │
                                        ▼
                              /plan-execute ──► /plan-archive
```

## Usage

### Pre-requisites

- [Claude Code](https://claude.ai/claude-code) CLI installed
- A `CLAUDE.md` file in your project root with your project standards

### Commands

1.  **`/plan-create`** - Create a new plan in `.planning/[plan name]/plan.md`. Asks for the plan name.
2. **`/plan-critique`** - Claude code reviews your plan. Generates a `critique.md` with issues and suggestions.
3. User decides which parts of critique are good for the plan and updates `plan.md`. Go back to 2.
4. **`/plan-execute`** - Execute your plan. Parses the plan into steps, asks for confirmation, and runs each step.
Supports resume on failure.
5. **`/plan-archive`** - Archive a completed plan. Moves it to the `.planning/archived/[plan name]` subfolder and deletes the original.

## Credits

Created by [Serban Ghita](https://github.com/serbanghita) under MIT License
