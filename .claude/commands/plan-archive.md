# Plan Archive Command

You are archiving the user's completed (or abandoned) plan folder for future reference.

## Instructions

1. **Check prerequisites:**
   - If `.claude/plan/plan.md` does not exist or is empty: Respond with "Nothing to archive. No plan found in .claude/plan/plan.md"

2. **Ensure archive directory exists:**
   - If `.claude/archived_plans/` does not exist, create it

3. **Extract metadata:**
   - Read `.claude/plan/plan.md` to get the plan title (first H1 heading)
   - If no title found, prompt user: "Please provide a title for this archived plan:"
   - Read `.claude/plan/critique.md` to get keywords (if available)
   - Read `.claude/plan/execution-log.md` to get execution status (if available)

4. **Generate archive folder name:**
   - Format: `YYYY-MM-DD_HH-MM-SS_[slug]/`
   - Slug: derived from title (lowercase, spaces to hyphens, max 50 chars, alphanumeric and hyphens only)
   - Example: `2026-01-12_14-30-00_add-user-authentication/`

5. **Create the archive folder** at `.claude/archived_plans/[folder-name]/`

6. **Copy contents from `.claude/plan/` to the archive folder:**

   **Include:**
   - `plan.md` (the original plan)
   - `execution-log.md` (if exists)
   - All other files (SQL queries, text snippets, images, etc.)

   **Exclude:**
   - `critique.md` (critique content is not archived)
   - `execution-state.json` (temporary state file)

7. **Add archive metadata** by creating `.claude/archived_plans/[folder-name]/archive-info.md`:

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

8. **Prompt user for cleanup:**
   ```
   Plan archived to: .claude/archived_plans/[folder-name]/

   Would you like me to clear the working folder for a new plan?
   This will:
   - Empty .claude/plan/plan.md
   - Delete .claude/plan/critique.md
   - Delete .claude/plan/execution-log.md (if exists)
   - Delete .claude/plan/execution-state.json (if exists)
   - Delete any other files in .claude/plan/

   Your archived copy is preserved.
   ```

9. **If user confirms cleanup:**
   - Delete all files in `.claude/plan/` except `plan.md`
   - Empty `plan.md` (keep the file but clear contents)
   - Respond: "Working folder cleared. You can start a new plan in .claude/plan/plan.md"

10. **If user declines cleanup:**
    - Keep all files as-is
    - Respond: "Archive complete. Working files preserved."

## Archive Without Execution

It is valid to archive a plan that was never executed. This is useful for:
- Abandoned plans
- Reference plans
- Plans superseded by other work

In this case, the execution status will be `NOT_EXECUTED`.

## Important

- Never include critique.md in the archive (only keywords are preserved in archive-info.md)
- Always include the full original plan and all supporting files
- Include execution log only if the plan was executed
- Use current timestamp for the archive folder name, not execution time
- Preserve the original file structure within the archived folder