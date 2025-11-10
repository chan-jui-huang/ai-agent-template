Commit staged files.

Create a git commit with detailed log using en-US.

On macOS, skip any in-sandbox Git writes and immediately switch to outside the sandbox. If switching fails, start writing the Git commit log, but do not execute git commit.

**When adding new lines to commit message, use single quote to multi-line text.**.

If there is a single quote in the message, use standard bash syntax to escape that
single quote.

Example:
```
git commit -m 'feat: new feature

- description of the feature 1
- description of the feature 2'\''s sub-feature
'
```