# Plan Archive Command

You are archiving the user's completed (or abandoned) plan for future reference.

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
     "No plans found. Nothing to archive."

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

     Which plan would you like to archive? [1-3]
     ```
   - If only one plan exists, auto-select it and inform user

5. **Check prerequisites:**
   - If `[plansFolder]/[selected-plan]/plan.md` does not exist or is empty:
     Respond with "Nothing to archive. Plan file is missing or empty."

6. **Ensure archive directory exists:**
   - If `[plansFolder]/archived/` does not exist, create it

7. **Extract metadata:**
   - Read `plan.md` to get the plan title (first H1 heading)
   - If no title found, use the folder name
   - Read `critique.md` to get keywords (if available)
   - Read `execution-log.md` to get execution status (if available)

8. **Generate archive folder name:**
   - Format: `YYYY-MM-DD_HH-MM-SS_[slug]/`
   - Slug: the plan folder name
   - Example: `2026-01-12_14-30-00_add-user-authentication/`

9. **Create the archive folder** at `[plansFolder]/archived/[folder-name]/`

10. **Copy contents from `[plansFolder]/[selected-plan]/` to the archive:**

    **Include:**
    - `plan.md` (the original plan)
    - `execution-log.md` (if exists)
    - All other files (SQL queries, text snippets, images, etc.)

    **Exclude:**
    - `critique.md` (critique content is not archived)
    - `execution-state.json` (temporary state file)

11. **Add archive metadata** by creating `archive-info.md` in the archive:

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

12. **Delete the original plan folder:**
    - Remove `[plansFolder]/[selected-plan]/` entirely
    - Update `.claude/plan-critique-config.json` to clear `currentPlan` if it was
      the archived plan

13. **Respond with confirmation:**
    ```
    Plan archived to: [plansFolder]/archived/[folder-name]/

    The original plan folder has been removed.
    Create a new plan with `/plan-create`.
    ```

## Archive Without Execution

It is valid to archive a plan that was never executed. This is useful for:
- Abandoned plans
- Reference plans
- Plans superseded by other work

In this case, the execution status will be `NOT_EXECUTED`.

## Important

- Never include critique.md in the archive (only keywords are preserved)
- Always include the full original plan and all supporting files
- Include execution log only if the plan was executed
- Use current timestamp for the archive folder name
- Preserve the original file structure within the archived folder
- Always delete the original plan folder after archiving
