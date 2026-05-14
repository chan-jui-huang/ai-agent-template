---
name: create-branch
description: Create and check out a meaningful git branch name from the staged changes.
compatibility: Requires git and a writable git worktree with staged changes.
user-invocable: true
---

# Create Branch Skill

Use `git` create a meaningful branch name following the changes of staged files.
When the branch is created, checkout to this branch.

## Branch Naming

Start the branch name with a Conventional Commits `<type>`.
Match the style already used by the project:

- If branches use `<type>/<summary>`, create names like `feat/add-login-form` or `fix/handle-empty-config`.
- If branches use `<type>/<author>/<summary>`, create names like `feat/ray/add-login-form` or `docs/ray/update-readme`.
- Use the type that best matches the staged changes. Examples include `feat`, `fix`, `docs`, `refactor`, `test`, and `chore`, but the allowed range is not limited to these examples; use types defined by Conventional Commits and types commonly used in software projects.
- Keep the summary short, lowercase, and hyphen-separated.
