---
name: github-repo-scaffold
description: Create or update professional GitHub repository scaffolding files. Use when the user asks to generate README, repository About text, CODEOWNERS, CONTRIBUTING, FUNDING, SECURITY, or other .github support files for a project.
---

# GitHub Repo Scaffold

Create clean GitHub project files that match the actual repository. Read the repo structure before writing and do not invent paths, workflows, checks, or protections.

## Required Outputs

When asked for full scaffolding, produce:

1. `README.md`
2. Repository About description
3. `.github/CODEOWNERS`
4. `.github/CONTRIBUTING.md`
5. `.github/FUNDING.yml`
6. `.github/SECURITY.md`

## Content Rules

- Start README with project name and one-sentence summary.
- Include quick install or usage, main features, useful architecture notes, contribution link, and license.
- Keep About text to 1-2 concise sentences with 3-6 meaningful keywords.
- Use GitHub CODEOWNERS syntax and default owner `@edyhvh` unless the user says otherwise.
- Use `github: [edyhvh]` for FUNDING unless the project has other platforms.
- Keep SECURITY realistic: mention only protections, checks, and workflows that exist or are explicitly requested.
- Default maintainer is Jhonny / `@edyhvh`.
- Default license is MIT unless told otherwise.
- Prefer clarity over length.
