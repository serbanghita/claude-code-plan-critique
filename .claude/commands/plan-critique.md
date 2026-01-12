# Plan Critique Command

You are performing an iterative review of the user's execution plan.

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

     Which plan would you like to critique? [1-3]
     ```
   - If only one plan exists, auto-select it and inform user

5. **Update current plan in settings:**
   - Update `.claude/plan-critique-config.json` to set `currentPlan` to the
     selected plan slug
   - Preserve all other settings

6. **Read the plan file** at `[plansFolder]/[selected-plan]/plan.md`

7. **Check for errors:**
   - If `plan.md` is empty: Respond with
     "Plan file is empty. Edit `[plansFolder]/[selected-plan]/plan.md`"
   - If `CLAUDE.md` does not exist in project root: Respond with
     "Create a CLAUDE.md file in the root of your project."

8. **Read the existing critique** at `[plansFolder]/[selected-plan]/critique.md`
   (if it exists) to determine the current iteration number.
   If no critique exists, this is iteration 1.

9. **Review supporting files** in the `[plansFolder]/[selected-plan]/` folder
   (if any exist). The user may have placed SQL queries, text snippets,
   images, or other reference materials alongside the plan.

10. **Perform a thorough critique** of the plan considering:
    - **Clarity**: Are requirements specific and unambiguous?
    - **Completeness**: Are all necessary steps included?
    - **Order**: Are dependencies between steps correctly sequenced?
    - **Feasibility**: Can each step be executed given the current codebase?
    - **Risk**: Are there potential side effects or breaking changes?
    - **Standards**: Does it comply with CLAUDE.md project standards?
    - **Scope**: Is scope reasonable? Any unnecessary additions?
    - **Testability**: How will success be verified?
    - **Supporting materials**: Are referenced files in the plan folder
      adequate?

11. **Write the critique** to `[plansFolder]/[selected-plan]/critique.md`
    using this exact format:

```markdown
# [Title extracted from first H1 in plan.md, or "Untitled Plan"]

> Keywords: [auto-generated comma-separated keywords based on plan content]

Iteration: [number]

## Summary

[Brief overview of the plan and overall assessment]

---

## [Issue Title]

**Description:**
[Clear, concise summary of the issue]

**Suggested Solution:**
[Suggested fix with all pertinent details]

```[language]
[code block if applicable]
```

---

[Repeat for each issue found]

[If no issues found:]
No issues found. Plan is ready for execution via `/plan-execute`.
```

## Context to Consider

When critiquing, analyze:
- Codebase structure (existing files, directories, patterns)
- Project standards from CLAUDE.md
- Dependencies (package.json, requirements.txt, etc.)
- Git state if relevant
- Whether referenced files/APIs actually exist
- Supporting files in the plan folder

## Important

- Always overwrite the previous critique (do not append)
- Increment the iteration number from the previous critique
- Be direct and constructive in feedback
- Suggest multiple solutions when appropriate
