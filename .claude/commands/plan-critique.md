---
allowed-tools: Read, Glob, Grep, Write, Edit, AskUserQuestion, LSP, Bash(git status:*), Bash(git log:*), Bash(git diff:*), Bash(echo $PPID), Bash(kill -0:*), Bash(rm:*), Bash(mkdir:*)
description: Critique the user's plan from plan.md
disable-model-invocation: true
---

You are performing an iterative review of the user's execution plan.
Think hard and critique the plan, code, architecture, system design, design patterns.

Always use the top model to guide your review.

To do this, follow these steps precisely:

1. Read `.claude/plan-critique-config.json`, get `plansFolder` path from settings. If the file doesn't exist or
   `plansFolder` is not set: Respond with "No plans folder configured. Run `/plan-create` first to set up."
2. Get the Claude Code process ID by running: `echo $PPID`. Store this as `sessionPID`.
3. Clean up stale sessions: Scan `[plansFolder]/.sessions/` for files. For each file named with a PID, check if that
   process is still running via `kill -0 [PID] 2>/dev/null`. If the command fails (process not running), delete that
   session file. This is non-blocking cleanup.
4. Read the current session's plan from `[plansFolder]/.sessions/[sessionPID]` if it exists. Store as `sessionPlan`.
5. Scan `[plansFolder]/` for subdirectories (each subdirectory is a plan). Exclude `archived/` and `.sessions/`
   folders and any files, only list plan directories.
   If no plan folders exist: Respond with "No plans found. Create one with `/plan-create`".
6. Ask the user to select a plan, present the list of available plans.
   If only one plan exists, auto-select it and inform user.
   If `sessionPlan` matches a plan folder, mark it as "(current session)".
   Example:
     ```
     Available plans:
     1. add-user-authentication (current session)
     2. refactor-database-layer
     3. implement-caching

     Which plan would you like to critique? [1-3]
     ```
7. Update the session file `[plansFolder]/.sessions/[sessionPID]` with the selected plan slug (create if needed).
8. Read the plan file at `[plansFolder]/[selected-plan]/plan.md`
9. Check for errors:
   - If `plan.md` is empty: Respond with "Plan file is empty. Edit `[plansFolder]/[selected-plan]/plan.md`"
   - If `CLAUDE.md` does not exist in project root: Respond with "Create a `CLAUDE.md` file in the root of your project."
10. Read the existing "Iteration: [number]" at `[plansFolder]/[selected-plan]/critique.md` (if it exists) and
    determine the current iteration number. If no critique exists, this is iteration 1.
11. If the plan references files that are in the `[plansFolder]/[selected-plan]/` folder, review those as well and
    add them to the context of the critique.
12. Perform a thorough critique of the plan considering:
    - Clarity: Are requirements specific and unambiguous?
    - Completeness: Are all necessary steps included?
    - Order: Are dependencies between steps correctly sequenced?
    - Feasibility: Can each step be executed given the current codebase?
    - Risk: Are there potential side effects or breaking changes?
    - Standards: Does it comply with `CLAUDE.md` project standards?
    - Scope: Is scope reasonable? Any unnecessary additions?
    - Testability: How will success be verified?
    - Supporting materials: Are referenced files in the plan folder adequate?
13. Evaluate whether the plan can be split into independent tasks. If the plan contains multiple features
    or changes that can be executed separately, strongly recommend splitting it into separate plans.
    Why this matters:
    - Reduces complexity during execution
    - Limits the context window needed for each plan
    - Makes critique iterations more focused and actionable
    - Allows independent tasks to proceed without blocking each other

    Example: A plan with "Add user authentication", "Refactor database layer", and "Add caching" should
    be split into three separate plans if these can be implemented independently.

    When suggesting a split, be specific about which sections should become their own plan.
14. Write the critique to `[plansFolder]/[selected-plan]/critique.md`.
    When writing the critique, follow the original chapters from `plan.md`.
    The goal is to be able to easily override the `plan.md` if the user chooses to merge `critique.md` with `plan.md`

    Use the following format suggested in the "Critique Format" chapter.

Notes:

- When critiquing, always analyze codebase structure (existing files, directories, patterns), Project standards from `CLAUDE.md`, The `README.md` file, dependencies (package.json, requirements.txt, etc.), git state if relevant, whether referenced files/APIs actually exist, supporting files in the plan folder.
- When doing the writeup of the critique, in the "Description" area make use of the line numbers from `plan.md` file and reference those, so that the user can easily find what text to replace/update.
- Use LSP to find classes, methods, references (mandatory when available; fallback to grep/search tools if unavailable)
- Add the found issues/observations list in the beginning of the critique.md file as a Table of contents
- Always follow the chapters from plan.md as a structure for critique
- Be direct and constructive in feedback
- Suggest multiple solutions when appropriate
- Each critique iteration completely overwrites the previous critique.md file.
- Discard addressed issues**: If an issue from the previous critique has been fixed in plan.md, do not include it.
- Only include current issues: The critique should reflect the current state of plan.md.
- New unrelated observations: If new issues appear that don't fit under existing plan.md chapters add them as new chapters at the bottom of the critique
- Increment iteration number: Always increment from the previous critique's iteration number

---

## Critique Format

```markdown
# [Title extracted from first H1 in plan.md, or "Untitled Plan"]
> Keywords: [auto-generated comma-separated keywords based on plan content]  
Iteration: [number]

## Summary

[Brief overview of the plan and overall assessment. Only use bullets, no formatting.]

---

## [Plan chapter title or Plan chapter title - specific issue]

Description:    
[Clear, concise summary of the issue]

Suggested Solution:    
[Suggested fix with all pertinent details]

    ```[language]
    [code block only if applicable, be brief]
    ```

---

[Repeat for each issue found]

[If no issues found:]
No issues found. Plan is ready for execution via `/plan-execute`.
```
