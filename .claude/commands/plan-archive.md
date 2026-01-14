---
allowed-tools: Read, Write, Edit, Glob, AskUserQuestion, Bash(mkdir:*), Bash(cp:*), Bash(mv:*), Bash(rm:*)
description: Archive a completed or abandoned plan for future reference
---

You are archiving the user's completed (or abandoned) plan for future reference.

To do this, follow these steps precisely:

1. Read `.claude/plan-critique-config.json` and get `plansFolder` path from settings.
   If the file doesn't exist or `plansFolder` is not set:
   Respond with "No plans folder configured. Run `/plan-create` first to set up."
2. Scan `[plansFolder]/` for subdirectories (each subdirectory is a plan).
   Exclude `archived/` folder and any files, only list plan directories.
   If no plan folders exist: Respond with "No plans found. Nothing to archive."
   If `currentPlan` is set and that folder exists, show it as default.
3. Ask the user to select a plan, present the list of available plans.
   If only one plan exists, auto-select it and inform user.
   If there's a current plan, mark it as "(current)".
   Example:
   ```
   Available plans:
   1. add-user-authentication (current)
   2. refactor-database-layer
   3. implement-caching

   Which plan would you like to archive? [1-3]
   ```
4. Check prerequisites. If `[plansFolder]/[selected-plan]/plan.md` does not exist or is empty:
   Respond with "Nothing to archive. Plan file is missing or empty."
5. Ensure `[plansFolder]/archived/` exists, create it if not.
6. Extract metadata:
   - Read `plan.md` to get the plan title (first H1 heading). If no title, use folder name.
   - Read `critique.md` to get keywords (if available)
   - Read `execution-log.md` to get execution status (if available)
7. Generate archive folder name using format: `YYYY-MM-DD_HH-MM-SS_[slug]/`
   Example: `2026-01-12_14-30-00_add-user-authentication/`
8. Create the archive folder at `[plansFolder]/archived/[folder-name]/`
9. Copy contents from `[plansFolder]/[selected-plan]/` to the archive:
   - Include: `plan.md`, `execution-log.md` (if exists), all other files (SQL, images, etc.)
   - Exclude: `critique.md`, `execution-state.json`
10. Create `archive-info.md` in the archive folder using the format in "Archive Info Format" below.
11. Delete the original plan folder `[plansFolder]/[selected-plan]/` entirely.
    Update `.claude/plan-critique-config.json` to clear `currentPlan` if it was the archived plan.
12. Respond with confirmation:
    ```
    Plan archived to: [plansFolder]/archived/[folder-name]/

    The original plan folder has been removed.
    Create a new plan with `/plan-create`.
    ```

Notes:

- Never include critique.md in the archive (only keywords are preserved in archive-info.md)
- Always include the full original plan and all supporting files
- Include execution log only if the plan was executed
- Use current timestamp for the archive folder name
- Preserve the original file structure within the archived folder
- Always delete the original plan folder after archiving
- It is valid to archive a plan that was never executed (abandoned, reference, or superseded plans).
  In this case, the execution status will be `NOT_EXECUTED`.

---

## Archive Info Format

Write to `[plansFolder]/archived/[folder-name]/archive-info.md`:

```markdown
# Archive Info

> Title: [Plan Title]
> Keywords: [comma-separated keywords from critique, or "none"]
> Archived: [YYYY-MM-DD HH:MM:SS]
> Execution Status: [COMPLETED | FAILED | PARTIAL | NOT_EXECUTED]

## Files Included

- plan.md
- [list other files that were archived]
```
