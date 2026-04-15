---
name: amend-into-commit
description: Amend staged changes into a target commit, defaulting to the latest commit on the current branch and updating only the commit description unless the user explicitly asks to change the title.
compatibility: Requires a git repository with staged changes.
metadata:
  version: "0.1.0"
---

# Amend Into Commit

Use this skill when the user wants to merge staged files into an existing commit instead of creating a new one.

## Workflow

1. Inspect `git status --short` and `git diff --cached` first.
2. Resolve the target commit:
   - If the user provides a commit hash, use that commit.
   - Otherwise, use the latest commit on the current branch with `git rev-parse HEAD`.
3. Review the target commit message with `git show --format=fuller --no-patch <commit>`.
4. Amend the staged changes into that commit.
5. Update the commit message rules:
   - If the user explicitly asks to change the commit title, update both title and description as requested.
   - Otherwise, keep the existing title and only extend or revise the description to include the staged changes.
6. After amend, report the new commit hash and note that it replaced the previous commit.

## Rules

- Never include unstaged files in the amend.
- Never modify git config.
- Avoid destructive git commands whenever possible.
- Prefer `git commit --amend` when the target is `HEAD`.
- If the user names an older non-HEAD commit, use a safe non-interactive history rewrite only when necessary and keep the result focused on the requested amend.
- Write commit messages in en-US.
- Do not mention AI tools, models, or agents in the commit message.
- Unless the user explicitly asks to modify the commit title, preserve the current title exactly.

## Example Prompts

```text
$amend-into-commit
Amend the staged files into the latest commit on the current branch and update the description to include these changes.
```

```text
$amend-into-commit
Amend the staged files into commit 1234abcd. Keep the title unchanged and update only the description.
```

```text
$amend-into-commit
Amend the staged files into the latest commit and change the title to feat(user): refine ttl defaults.
```
