# Plan Execute Command

You are executing the user's plan that has been iteratively refined.

## Instructions

1. **Read settings and get plans folder:**
   - Read `.claude/plan-critique-config.json`
   - Get `plansFolder` path from settings
   - If file doesn't exist or `plansFolder` is not set: Respond with
     "No plans folder configured. Run `/plan-create` first to set up."

2. **List available plans:**
   - Scan `[plansFolder]/` for subdirectories (each subdirectory is a plan)
   - Exclude `archived/` folder and any files, only list plan directories
   - If no plan folders exist: Respond with
     "No plans found. Create one with `/plan-create`"

3. **Check for current plan in settings:**
   - If `currentPlan` is set and that folder exists, show it as default

4. **Ask user to select a plan:**
   - Present the list of available plans
   - If there's a current plan, mark it as "(current)"
   - Example:
     ```
     Available plans:
     1. add-user-authentication (current)
     2. refactor-database-layer
     3. implement-caching

     Which plan would you like to execute? [1-3]
     ```
   - If only one plan exists, auto-select it and inform user

5. **Update current plan in settings:**
   - Update `.claude/plan-critique-config.json` to set `currentPlan` to the
     selected plan slug
   - Preserve all other settings

6. **Check prerequisites:**
   - If `[plansFolder]/[selected-plan]/plan.md` does not exist: Respond with
     "No plan.md found in this folder."
   - If `plan.md` is empty: Respond with
     "Plan file is empty. Please add your plan and run /plan-critique first."

7. **Check for existing execution state:**
   - If `[plansFolder]/[selected-plan]/execution-state.json` exists, read it
     and prompt the user:
     "Previous execution found at step [X]. Resume or restart?"
   - Wait for user response before proceeding

8. **Review supporting files** in the `[plansFolder]/[selected-plan]/` folder.
   The user may have placed SQL queries, text snippets, images, or other
   reference materials that are relevant to execution.

9. **Parse the plan** into discrete, executable steps

10. **Present the steps** to the user for confirmation:
    ```
    I've parsed your plan into the following steps:
    1. [Step description]
    2. [Step description]
    ...

    Do you want me to proceed with execution?
    ```

11. **Execute each step sequentially:**
    - Before each step, update execution-state.json with current progress
    - Execute the step
    - Log results to execution-log.md
    - On error: Stop execution, save state, and report the failure

12. **Generate execution log** at
    `[plansFolder]/[selected-plan]/execution-log.md` using this format:

```markdown
# Execution Log: [Plan Title]

Started: [YYYY-MM-DD HH:MM:SS]

---

## Step 1: [Step description]

**Result:** [COMPLETED | FAILED | SKIPPED]

**Output:**
[relevant output, changes made, or error messages]

---

## Step 2: [Step description]
...

---

## Summary

- Total steps: [X]
- Completed: [Y]
- Failed: [Z]
- Skipped: [W]

[If FAILED or PARTIAL:]
Execution stopped at step [N]. Run `/plan-execute` to resume.
```

13. **Execution state file**
    (`[plansFolder]/[selected-plan]/execution-state.json`) format:

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

14. **On successful completion:**
    - Update execution log with final summary
    - Delete execution-state.json
    - Inform user:
      "Plan executed successfully. Run `/plan-archive` to archive this plan."

## Important

- Always pause on errors - never continue past a failed step automatically
- Keep the execution log updated in real-time
- Be explicit about what changes are being made at each step
- If resuming, skip already-completed steps
- Reference any supporting files in the plan folder as needed during execution
