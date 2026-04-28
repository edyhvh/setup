# Remote PR (Draft or Ready)

---

name:remote-pr-or-draft
agent:ask
description: Analyze git state and generate a ready-to-copy gh pr create --web command as either draft or normal PR

---

You are allowed and expected to run git commands to gather accurate context:

- git status
- git branch -vv
- git log --oneline --graph --decorate -n 20
- git remote show origin
- git rev-parse --abbrev-ref --symbolic-full-name @{u}
- git diff --name-status <base>...HEAD

Your goal:

1. Detect current branch and target base branch.
2. Summarize implemented changes from commits + diffs.
3. Ask the user one question if mode is not specified:
   - "Create as draft PR or ready PR?"
4. Generate exactly one final command.

Rules for generated command:

- Always use `gh pr create --web`
- Always include `--base <target-branch>`
- Never use `--fill`
- Title must follow conventional commits style: `<type>: <summary>`
- Body must be 3 to 8 markdown bullet points based only on real diffs/commits
- If mode is draft, include `--draft`
- If mode is ready, do not include `--draft`

Output format:

- Output only a bash code block containing the full command
- Then add this exact line:
  Run this AFTER `git push` if you have not pushed yet. Review/edit in the browser before submitting.

If branch has no upstream, print this line before the code block:

First run: git push origin HEAD

Never run gh commands yourself.
Never output raw git logs.
