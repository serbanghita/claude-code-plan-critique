# Plan Execute Command

You are executing the user's plan that has been iteratively refined.

## Instructions

1. **Check prerequisites:**
   - If `.claude/plan/plan.md` does not exist: Respond with "No plan found. Create a plan in .claude/plan/plan.md and run /plan-critique first."
   - If `.claude/plan/plan.md` is empty: Respond with "Plan file is empty. Please add your plan and run /plan-critique first."

2. **Check for existing execution state:**
   - If `.claude/plan/execution-state.json` exists, read it and prompt the user:
     "Previous execution found at step [X]. Would you like to resume or restart?"
   - Wait for user response before proceeding

3. **Check critique status:**
   - Read `.claude/plan/critique.md` if it exists
   - If Status is `NOT_READY` or `NEEDS_MINOR_CHANGES`, warn the user:
     "Warning: The critique shows unresolved issues (Status: [status]). Are you sure you want to proceed?"
   - Wait for user confirmation before proceeding

4. **Review supporting files** in the `.claude/plan/` folder. The user may have placed SQL queries, text snippets, images, or other reference materials that are relevant to execution.

5. **Parse the plan** into discrete, executable steps

6. **Present the steps** to the user for confirmation:
   ```
   I've parsed your plan into the following steps:
   1. [Step description]
   2. [Step description]
   ...

   Do you want me to proceed with execution?
   ```

7. **Execute each step sequentially:**
   - Before each step, update `.claude/plan/execution-state.json` with current progress
   - Execute the step
   - Log results to `.claude/plan/execution-log.md`
   - On error: Stop execution, save state, and report the failure

8. **Generate execution log** at `.claude/plan/execution-log.md` using this format:

```markdown
# Execution Log: [Plan Title]

Started: [YYYY-MM-DD HH:MM:SS]
Status: [IN_PROGRESS | COMPLETED | FAILED | PARTIAL]

---

## Step 1: [Step description]

**Status:** [COMPLETED | FAILED | SKIPPED]
**Duration:** [Xs]

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

9. **Execution state file** (`.claude/plan/execution-state.json`) format:

```json
{
  "planTitle": "[title]",
  "totalSteps": 5,
  "currentStep": 2,
  "completedSteps": [0, 1],
  "failedStep": null,
  "startedAt": "2026-01-12T10:30:00Z",
  "lastUpdated": "2026-01-12T10:35:00Z",
  "status": "in_progress"
}
```

10. **On successful completion:**
    - Update execution log status to COMPLETED
    - Delete `.claude/plan/execution-state.json`
    - Inform user: "Plan executed successfully. Run `/plan-archive` to archive this plan."

## Important

- Always pause on errors - never continue past a failed step automatically
- Keep the execution log updated in real-time
- Be explicit about what changes are being made at each step
- If resuming, skip already-completed steps
- Reference any supporting files in `.claude/plan/` as needed during execution