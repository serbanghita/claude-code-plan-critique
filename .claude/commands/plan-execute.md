---
allowed-tools: Read, Write, Edit, Glob, Grep, AskUserQuestion, LSP, Bash, Bash(echo $PPID), Bash(kill -0:*), Bash(rm:*)
description: Execute the user's plan that has been iteratively refined
---

You are executing the user's plan that has been iteratively refined.

To do this, follow these steps precisely:

1. Read `.claude/plan-critique-config.json` and get `plansFolder` path from settings.
   If the file doesn't exist or `plansFolder` is not set:
   Respond with "No plans folder configured. Run `/plan-create` first to set up."
2. Get the Claude Code process ID by running: `echo $PPID`. Store this as `sessionPID`.
3. Clean up stale sessions: Scan `[plansFolder]/.sessions/` for files. For each file named with a PID, check if that
   process is still running via `kill -0 [PID] 2>/dev/null`. If the command fails (process not running), delete that
   session file. This is non-blocking cleanup.
4. Read the current session's plan from `[plansFolder]/.sessions/[sessionPID]` if it exists. Store as `sessionPlan`.
5. Scan `[plansFolder]/` for subdirectories (each subdirectory is a plan).
   Exclude `archived/` and `.sessions/` folders and any files, only list plan directories.
   If no plan folders exist: Respond with "No plans found. Create one with `/plan-create`".
6. Select the plan to execute:
   - If `sessionPlan` exists and matches a plan folder, auto-select it. Inform the user:
     "Using current session plan: [sessionPlan]"
   - Else if only one plan exists, auto-select it and inform user.
   - Otherwise, ask the user to select a plan from the list.
     Example:
     ```
     Available plans:
     1. add-user-authentication
     2. refactor-database-layer
     3. implement-caching

     Which plan would you like to execute? [1-3]
     ```
7. Update the session file `[plansFolder]/.sessions/[sessionPID]` with the selected plan slug (create if needed).
8. Check prerequisites:
   - If `[plansFolder]/[selected-plan]/plan.md` does not exist: Respond with "No plan.md found."
   - If `plan.md` is empty: Respond with "Plan file is empty. Run /plan-critique first."
9. Check for existing execution state. If `[plansFolder]/[selected-plan]/execution-state.json` exists,
   read it and prompt: "Previous execution found at step [X]. Resume or restart?"
   Wait for user response before proceeding.
10. Review supporting files in the `[plansFolder]/[selected-plan]/` folder. The user may have placed
    SQL queries, text snippets, images, or other reference materials relevant to execution.
11. Parse the plan into discrete, executable steps using this ordering strategy:
   - Independent tasks first: changes with no dependencies on other changes
   - Small to large: within independent tasks, order from smallest to largest scope
   - Dependent tasks after: once all independent tasks are ordered, add tasks that depend on them
   - Business priority: for tasks at the same dependency level, prioritize by business impact

   Example: If a plan has "Add utility function", "Create database migration", and "Update API endpoint
   (uses utility)", order as: 1) Add utility function, 2) Create database migration, 3) Update API endpoint
12. Present the steps to the user for confirmation:
    ```
    I've parsed your plan into the following steps:
    1. [Step description]
    2. [Step description]
    ...

    Do you want me to proceed with execution?
    ```
13. Execute each step sequentially:
    - Before each step, update execution-state.json with current progress
    - Ask for explicit user permission before high-risk operations:
      - Database migrations or schema changes
      - Deleting files or directories
      - Modifying configuration files
      - External API calls with side effects
      - Any irreversible operations
    - Execute the step
    - Log results to execution-log.md
    - On error: Stop execution, save state, and report the failure
14. On successful completion:
    - Update execution log with final summary
    - Delete execution-state.json
    - Inform user: "Plan executed successfully. Run `/plan-archive` to archive this plan."

Notes:

- Always pause on errors, never continue past a failed step automatically
- Keep the execution log updated in real-time
- Be explicit about what changes are being made at each step
- If resuming, skip already-completed steps
- Reference any supporting files in the plan folder as needed during execution
- If there's any functional or architectural change that needs to be mentioned or updated in the project's `CLAUDE.md` file, ask the user for acknowledgement after the plan has been executed giving a very brief summary of changes.

---

## Execution Log Format

Write to `[plansFolder]/[selected-plan]/execution-log.md`:

```markdown
# Execution Log: [Plan Title]

Started: [YYYY-MM-DD HH:MM:SS]

---

## Step 1: [Step description]

Result: [COMPLETED | FAILED | SKIPPED]

Output:
[relevant output, changes made, or error messages]

---

## Summary

- Total steps: [X]
- Completed: [Y]
- Failed: [Z]
- Skipped: [W]

[If failed or partial:]
Execution stopped at step [N]. Run `/plan-execute` to resume.
```

---

## Execution State Format

Write to `[plansFolder]/[selected-plan]/execution-state.json`:

```json
{
  "planTitle": "[title]",
  "totalSteps": 5,
  "currentStep": 2,
  "completedSteps": [0, 1],
  "failedStep": null,
  "startedAt": "2026-01-12T10:30:00Z",
  "lastUpdated": "2026-01-12T10:35:00Z"
}
```
