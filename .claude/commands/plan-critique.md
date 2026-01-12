# Plan Critique Command

You are performing an iterative review of the user's execution plan.

## Instructions

1. **Read the plan file** at `.claude/plan/plan.md`

2. **Check for errors:**
   - If `.claude/plan/plan.md` does not exist: Create the directory and an empty file, then respond with "Plan file created at .claude/plan/plan.md. Please fill it with your plan and run /plan-critique again."
   - If `.claude/plan/plan.md` is empty: Respond with "You need to fill the plan first. See .claude/plan/plan.md file"
   - If `CLAUDE.md` does not exist in the project root: Respond with "You need to create a CLAUDE.md file in the root of your project."

3. **Read the existing critique** at `.claude/plan/critique.md` (if it exists) to determine the current iteration number. If no critique exists, this is iteration 1.

4. **Review supporting files** in the `.claude/plan/` folder (if any exist). The user may have placed SQL queries, text snippets, images, or other reference materials alongside the plan. Consider these when evaluating feasibility and completeness.

5. **Perform a thorough critique** of the plan considering:
   - **Clarity**: Are requirements specific and unambiguous?
   - **Completeness**: Are all necessary steps included?
   - **Order**: Are dependencies between steps correctly sequenced?
   - **Feasibility**: Can each step be executed given the current codebase?
   - **Risk**: Are there potential side effects or breaking changes?
   - **Standards**: Does it comply with CLAUDE.md project standards?
   - **Scope**: Is scope reasonable? Any unnecessary additions?
   - **Testability**: How will success be verified?
   - **Supporting materials**: Are referenced files in the plan folder adequate?

6. **Determine the plan status:**
   - `NOT_READY`: Has Critical or Major issues
   - `NEEDS_MINOR_CHANGES`: Only Minor issues remain
   - `READY_TO_EXECUTE`: No issues found

7. **Write the critique** to `.claude/plan/critique.md` using this exact format:

```markdown
# [Title extracted from first H1 in plan.md, or "Untitled Plan"]

> Keywords: [auto-generated comma-separated keywords based on plan content]

Iteration: [number]
Status: [NOT_READY | NEEDS_MINOR_CHANGES | READY_TO_EXECUTE]

## Summary

- Critical issues: [count]
- Major issues: [count]
- Minor issues: [count]

[If READY_TO_EXECUTE:]
No blocking issues found. Plan is ready for execution via `/plan-execute`.

---

## [Issue Title]

**Severity:** [Critical | Major | Minor]

**Description:**
[Clear, concise summary of the issue]

**Proposed Solution:**
[Suggested fix with all pertinent details]

```[language]
[code block if applicable]
```

---

[Repeat for each issue found]
```

## Issue Severity Definitions

- **Critical**: Blocks execution or could cause data loss/corruption
- **Major**: Significant problems but workarounds may exist
- **Minor**: Style, optimization, or nice-to-have improvements

## Context to Consider

When critiquing, analyze:
- Codebase structure (existing files, directories, patterns)
- Project standards from CLAUDE.md
- Dependencies (package.json, requirements.txt, etc.)
- Git state if relevant
- Whether referenced files/APIs actually exist
- Supporting files in `.claude/plan/` folder

## Important

- Always overwrite the previous critique (do not append)
- Increment the iteration number from the previous critique
- Be direct and constructive in feedback
- Suggest multiple solutions when appropriate