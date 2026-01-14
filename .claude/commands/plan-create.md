---
allowed-tools: Read, Write, Edit, Glob, AskUserQuestion, Bash(mkdir:*)
description: Create a new plan folder with a plan.md template
---

You are creating a new plan folder for the user.

To do this, follow these steps precisely:

1. Read `.claude/plan-critique-config.json` and get `plansFolder` path from settings.
   If the file doesn't exist or `plansFolder` is not set:
   - Ask the user: "Where would you like to store your plans? Provide a folder path (e.g., `.claude/plans`):"
   - Save the path as `plansFolder` in `.claude/plan-critique-config.json`
   - Create the folder if it doesn't exist
   - Create an `archived/` subfolder inside it
2. Ask the user directly (do NOT offer predefined options): "Enter a name for this plan:"
   The user must provide a custom name with at least 3 characters.
   If invalid, respond with "Plan name must be at least 3 characters." and ask again.
3. Generate a slug from the plan name:
   - Convert to lowercase
   - Replace spaces with hyphens
   - Remove characters that are not alphanumeric or hyphens
   - Trim to max 50 characters
   - Example: "Add User Authentication" becomes `add-user-authentication`
4. Check if `[plansFolder]/[slug]/` already exists.
   If it does: Respond with "A plan with this name already exists at `[plansFolder]/[slug]/`. Choose a different name."
   Ask for a new name and repeat from step 3.
5. Create directory `[plansFolder]/[slug]/`
6. Update `.claude/plan-critique-config.json` to set `currentPlan` to the new slug. Preserve all other settings.
7. Create the plan template at `[plansFolder]/[slug]/plan.md` using the format in "Plan Template" section below.
8. Respond with confirmation:
   ```
   Created new plan: [plansFolder]/[slug]/
   Edit your plan at: [plansFolder]/[slug]/plan.md
   When ready, run `/plan-critique` to review your plan.
   ```

Notes:

- The slug must be filesystem-safe (no special characters)
- Keep the original plan name with proper casing in the H1 heading of plan.md
- Only create plan.md initially (critique.md is created by `/plan-critique`)
- Always use `plansFolder` from settings as the base directory

---

## Plan Template

```markdown
# [Original plan name with proper casing]

Describe what you want to achieve. Be specific.
Split your specifications by Module, Model, Chapters, Subchapters so they can be addressed in the critique phase.

## Chapter 1 (rename this)

Description of what you are trying to achieve.
```
