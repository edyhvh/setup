# Sources and Deduplication Map

## Active sources checked in this sync

- `~/davar`
- `~/nave`
- `~/qahal`
- `~/.codex`
- `~/.kimi`
- `~/.claude` (safe settings only)

## Source unavailable in this sync

- `~/shafan` (path not found)

## Shared duplicates (single canonical copy kept)

- `.github/prompts/remote.prompt.md`
  - same in: davar, nave, qahal
  - canonical source: `davar`
- `.github/skills/gh-cli/SKILL.md`
  - same in: davar, nave, qahal
  - canonical source: `davar`
- `.github/skills/refactor-pro/SKILL.md`
  - same in: davar, nave, qahal
  - canonical source: `davar`
- `.github/skills/xai/SKILL.md`
  - same in: davar, nave, qahal
  - canonical source: `davar`
- `.github/instructions/bun.instructions.md`
  - same in: davar, nave, qahal
  - canonical source: `davar`
- `.github/prompts/github.prompt.md`
  - same in: davar, nave, qahal
  - canonical source: `davar`
- `.github/prompts/issue.prompt.md`
  - same in: nave, qahal
  - canonical source: `nave`

## Consolidated prompts

- `.github/prompts/remote-pr-or-draft.prompt.md`
  - merged into: `.github/prompts/remote.prompt.md`
  - reason: same PR-command workflow, with draft-or-ready mode handled in one canonical prompt

## Donations

- `.github/FUNDING.yml`
  - selected source: `davar`
  - reason: includes both `github` and `ko_fi`

## Unique instruction templates included

- `.github/instructions/davar.instructions.md` (from davar)
- `.github/instructions/nave.instructions.md` (from nave)
- `.github/instructions/qahal.instructions.md` (from qahal)
- `.github/instructions/skills.required.md` (from qahal)
- `.github/instructions/stack.instructions.md` (from qahal)
- `.github/instructions/telegram.instructions.md` (from qahal)

## Unique skills included

- `.github/skills/expo/SKILL.md` (from davar)
- `.github/skills/seo/SKILL.md` (from davar)
- `.github/skills/typer-cli/SKILL.md` (from nave)
- `.github/skills/refactor-pro/SKILLS.md` (from nave/qahal)
- `.github/skills/git-pr-command/` (from ~/.codex)
- `.github/skills/github-issue-draft/` (from ~/.codex)
- `.github/skills/github-repo-scaffold/` (from ~/.codex)
- `.github/skills/paper-to-nativewind/` (standardized from cross-assistant setup)

## Kimi assets included

- `.kimi/config.toml` (from ~/.kimi)
- `.kimi/mcp.json` (from ~/.kimi)
- `.kimi/skills/paper-to-nativewind/` (standardized from cross-assistant setup)

## Claude assets included

- `.claude/settings.local.json` (from ~/.claude)

## Common rule assets included

- `.cursor/rules/bun.mdc` (from davar)
- `.cursor/rules/design.mdc` (from davar)

## Security filtering

- Excluded secrets and machine-local credentials from source homes, including:
  - `~/.claude/settings.json` with embedded provider tokens
  - auth/session/log/cache/telemetry state from Claude, Codex, and Kimi directories
