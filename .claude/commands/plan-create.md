# Plan Create Command

You are creating a new plan folder for the user.

## Instructions

1. **Check for plans folder configuration:**
   - Read `.claude/plan-critique-config.json`
   - If file doesn't exist or `plansFolder` is not set, this is the first
     execution:
     - Ask the user: "Where would you like to store your plans?
       Please provide a folder path (e.g., `.claude/plans` or `docs/plans` or `planning`):"
     - Wait for user response
     - Save the path as `plansFolder` in `.claude/plan-critique-config.json`
     - Create the folder if it doesn't exist
     - Create an `archived/` subfolder inside it for archived plans

2. **Ask for the plan name:**
   - Ask the user directly (do NOT offer predefined options): "Enter a name for this plan:"
   - The user must provide a custom name (at least 3 characters)
   - If the name is less than 3 characters, respond with "Plan name must be at least 3 characters." and ask again
   - Wait for user response

3. **Generate folder name (slug):**
   - Convert to lowercase
   - Replace spaces with hyphens
   - Remove any characters that are not alphanumeric or hyphens
   - Trim to max 50 characters
   - Example: "Add User Authentication" → `add-user-authentication`

4. **Check for existing plan:**
   - If `[plansFolder]/[slug]/` already exists: Respond with
     "A plan with this name already exists at `[plansFolder]/[slug]/`.
     Choose a different name or delete the existing folder."
   - Ask for a new name and repeat from step 3

5. **Create the plan folder:**
   - Create directory `[plansFolder]/[slug]/`

6. **Update current plan in settings:**
   - Update `.claude/plan-critique-config.json` to set `currentPlan` to the new slug
   - Preserve all other settings

7. **Create the plan template** at `[plansFolder]/[slug]/plan.md`:

```markdown
# [Original plan name with proper casing]

Describe what you want to achieve. Be as specific as possible.
We suggest splitting your specifications by Module, Model, Chapters, Subchapters so they can be addressed
specifically in the critique and user review phase.
```

8. **Respond with confirmation:**
   ```
   ╔═════════════════╗
   ║ Plan & critique ║
   ╚═════════════════╝

   Created new plan: [plansFolder]/[slug]/
   Edit your plan at: [plansFolder]/[slug]/plan.md
   When ready, run `/plan-critique` to review your plan.
   ```

## Important

- The slug must be filesystem-safe (no special characters)
- Keep the original plan name (with proper casing) in the H1 heading
- Only create the plan.md file initially (critique.md is created by
  /plan-critique)
- Always use the `plansFolder` path from settings as the base directory
