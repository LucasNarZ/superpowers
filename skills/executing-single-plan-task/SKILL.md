---
name: executing-single-plan-task
description: Use when executing one numbered task from an approved implementation plan in a fresh context
---

# Executing Single Plan Task

## Overview

Execute exactly one requested task from an approved implementation plan, then stop. This workflow preserves context by making each task a fresh-session unit with human approval before commit.

**Announce at start:** "I'm using the executing-single-plan-task skill to execute one task from this plan."

## Required Inputs

- Task number, provided by the human partner.
- Plan file path, provided by the human partner.

If either input is missing, ask for it before reading files or editing code.

## Process

1. Read the plan file.
2. Locate the requested `### Task N:` section.
3. Read enough surrounding plan context to understand dependencies, but keep execution scoped to Task N.
4. Before editing, stop and ask if:
   - Task N is missing.
   - Task N appears already complete.
   - Task N depends on incomplete prior tasks.
   - The task instructions conflict with repository or user instructions.
   - The task requires a git operation, package install, destructive operation, or write outside the current project without permission.
5. Execute only Task N's steps.
6. Run Task N's verification commands.
7. Report a concise summary with changed files and verification results. Do not print a full diff unless asked.
8. Ask for explicit human approval before considering the task complete.
9. After approval, ask permission for the git commit if not already granted.
10. Commit only the files for Task N.
11. Stop after the commit and tell the human partner to reset context before running the next `/execute-task` command.

## Approval Gate

Human approval is a hard gate after verification and before commit.

Use this message shape:

```text
Task N is implemented and verified.

Changed files:
- path/to/file

Verification:
- `command`: passed

Please review with git and tell me whether to commit this task.
```

Reviewing with git is sufficient. Do not include a full diff unless the human partner asks for one.

## Commit Rules

- Do not commit before explicit approval.
- Do not commit unrelated files.
- Use the commit command specified in Task N when present.
- If Task N does not specify a commit command, propose a conventional commit message and ask before committing.
- After a successful commit, stop. Do not start another task.

## Stop Conditions

Stop immediately when:

- The requested task cannot be found.
- The task is ambiguous or contradicted by current repository state.
- Verification fails repeatedly.
- The human partner does not approve the task.
- A required permission is missing.
- Completing the task requires work outside Task N.

## Red Flags

These thoughts mean you are about to violate the workflow:

| Thought | Reality |
|---|---|
| "I'll also do the next small task" | One invocation executes exactly one task. Stop. |
| "This cleanup is obvious" | If it is not in Task N, ask first. |
| "The diff would be helpful" | The human reviews with git unless they ask for a diff. |
| "I'll commit while waiting" | Commit only after approval. |
| "No need to reset context" | The reset is the point of this workflow. Stop after commit. |
| "A subagent would be cleaner" | This workflow intentionally does not use subagents. |

## Completion Message

After a successful commit, say:

```text
Task N committed. Reset context before continuing, then run:

/execute-task <next-task-number> <plan-file>
```
