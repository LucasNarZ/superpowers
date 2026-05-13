---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write implementation plans that give clear direction without doing the implementation work. Document the goal, architecture, files to create or modify, task boundaries, verification approach, and any small examples needed to clarify intent. Do not write full production code or complete tests in the plan. Give enough context for an engineer to implement confidently while leaving the implementation to the execution phase. DRY. YAGNI. TDD. Human approval before each task is considered complete.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Save plans to:** `docs/superpowers/plans/YYYY-MM-DD-<feature-name>.md`
- (User preferences for plan location override this default)

Plans are working documents. Do not commit the plan unless your human partner explicitly asks you to.

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained, functional, verifiable changes that make sense independently.

## Task Granularity

Each task should be one coherent, independently verifiable change. Steps should describe the work to do, the key design constraints, and how to verify it.

Use steps like:
- "Add tests covering the new behavior" - include test intent and key cases, not full test bodies
- "Implement the feature in the target module" - include responsibilities, inputs, outputs, and edge cases
- "Run focused tests" - include exact command and expected result
- "Ask for human approval" - include the approval message
- "Commit after approval" - include files to stage and commit message

Do not split plans into tiny mechanical edits. The implementer should make engineering decisions during execution.

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.
> **Checkpoint workflow:** If the human partner wants one task per fresh context, use `/execute-task <task-number> <plan-file>` instead. Execute exactly one task, verify it, ask for approval, commit after approval, then stop.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Add focused tests for the behavior**

Cover:
- The main success path
- The edge case that previously failed or is most likely to regress
- Any integration boundary touched by this task

Small example, only if needed to clarify shape:

```python
assert function(representative_input) == expected_result
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL because the behavior is not implemented yet

- [ ] **Step 3: Implement the behavior**

In `src/path/file.py`, add the logic that:
- Accepts `[input contract]`
- Returns `[output contract]`
- Handles `[important edge case]`
- Follows the existing pattern used by `[nearby function/module]`

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Ask for human approval**

Message: `Task N is implemented and verified. Please review the changes in git and tell me whether to proceed.`

- [ ] **Step 6: Commit after approval**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

## No Placeholders

Every step must contain enough direction for an engineer to act without guessing. These are **plan failures** — never write them:
- "TBD", "TODO", "implement later", "fill in details"
- "Add appropriate error handling" / "add validation" / "handle edge cases"
- "Write tests for the above" (without naming the behaviors and cases to cover)
- "Similar to Task N" (repeat the relevant direction — the engineer may be reading tasks out of order)
- Steps that describe what to do without naming files, responsibilities, inputs, outputs, or verification
- References to types, functions, or methods not defined in any task

## Code In Plans

Plans are not implementation patches. Do not write complete production code, complete test files, or large code blocks.

Allowed:
- Small snippets that clarify an interface, assertion shape, command, config key, or data structure
- Function signatures when the exact contract matters
- Pseudocode for non-obvious algorithms

Not allowed:
- Full function bodies that solve the task
- Complete test cases when a list of behaviors is enough
- Copy-paste-ready files
- Repeating code across tasks to avoid placeholders

## Remember
- Exact file paths always
- Direction over implementation: files, responsibilities, contracts, edge cases, and verification
- Small code examples only when they clarify intent better than prose
- Exact commands with expected output
- DRY, YAGNI, TDD
- Each task ends with human approval before commit

## Self-Review

After writing the complete plan, look at the spec with fresh eyes and check the plan against it. This is a checklist you run yourself — not a subagent dispatch.

**1. Spec coverage:** Skim each section/requirement in the spec. Can you point to a task that implements it? List any gaps.

**2. Placeholder scan:** Search your plan for red flags — any of the patterns from the "No Placeholders" section above. Fix them.

**3. Type consistency:** Do the types, method signatures, and property names you used in later tasks match what you defined in earlier tasks? A function called `clearLayers()` in Task 3 but `clearFullLayers()` in Task 7 is a bug.

If you find issues, fix them inline. No need to re-review — just fix and move on. If you find a spec requirement with no task, add the task.

## Execution Handoff

After saving the plan, hand off directly to execution:

**"Plan complete and saved to `docs/superpowers/plans/<filename>.md`. Next step: use superpowers:executing-plans to execute the plan continuously, or use `/execute-task <task-number> docs/superpowers/plans/<filename>.md` in a fresh context when you want one approved task per commit."**
