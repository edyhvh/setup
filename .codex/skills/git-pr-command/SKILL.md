---
name: git-pr-command
description: Generate a ready-to-copy gh pr create --web command from recent git history and diffs. Use when the user asks for a PR command, PR body, remote prompt, or help creating a pull request without executing gh commands.
---

# Git PR Command

Analyze git state and output only the final PR command plus the required note.

## Gather Context

Run read-only git commands as needed:

- `git status`
- `git branch -vv`
- `git log --oneline --graph --decorate -n 15`
- `git remote show origin`
- `git rev-parse --abbrev-ref --symbolic-full-name @{u}`
- `git diff`
- `git diff --stat`

Use the output to identify the current branch, tracking branch, likely base branch, commits since divergence, and actual file changes.

## Output Rules

- Never execute `gh`.
- Always generate `gh pr create --web`.
- Include `--base <target-branch>`.
- Do not use `--fill`.
- Use a conventional commits title: `<type>: <concise summary>`.
- Write a Markdown body with 3-8 bullets based only on commits and diffs.
- If the branch has no upstream or appears unpushed, include `First run: git push origin HEAD` before the command.

After the command, add exactly:

```text
Run this AFTER `git push` if you haven't pushed yet. Review/edit in the browser before submitting.
```
