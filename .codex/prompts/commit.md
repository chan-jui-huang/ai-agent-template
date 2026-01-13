Commit staged files. **Don't ask me any questions.**

Directly commit all staged files; do not append any paths after `git commit`.

Create a git commit with a detailed log using en-US.

On macOS, skip any in-sandbox Git writes and immediately switch to outside the sandbox. If switching fails, start writing the Git commit log, but do not execute git commit.

Do **not** mention any AI tools, models, or agents in the commit message
(e.g., do not include names like Claude, Codex, Gemini, ChatGPT, etc.).
The commit message must only describe the changes made in the code or files.

Do **not** add any co-author or attribution footer lines using phrasing such as
"Co-Authored-By", "Authored-By", "Generated-By", "Created-With", or any similar
attribution-style tags, unless the user explicitly provides the exact text.

**When adding new lines to the commit message, use single quotes for multi-line text.**

If there is a single quote in the message, use standard bash syntax to escape that
single quote.

Example:
```
git commit -m 'feat: new feature

- description of the feature 1
- description of the feature 2'\''s sub-feature
'
```