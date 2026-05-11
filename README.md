# Copilot Setup Compilation

This repository is a reusable baseline of shared AI setup assets collected from:

- ~/davar
- ~/nave
- ~/qahal
- ~/.codex
- ~/.kimi
- ~/.claude (safe settings only)

Unavailable during this sync:

- ~/shafan

It keeps one canonical copy of duplicate files and preserves unique reusable assets.

## Included

- GitHub instructions in `.github/instructions/`
- GitHub prompts in `.github/prompts/`
- Donations config (`GitHub Sponsors + Ko-fi`) in `.github/FUNDING.yml`
- All discovered skills in `.github/skills/`
- Common Cursor rule assets in `.cursor/rules/`
- Claude settings template in `.claude/`
- Codex config and native skills in `.codex/`
- Kimi config and skills in `.kimi/`
- Versioned zsh aliases in `shell/aliases.zsh`

## Prompt Focus (GitHub remote / PR)

Use:

- `.github/prompts/remote.prompt.md` for PR command generation, including draft-vs-ready selection

## Notes

- Duplicate files were deduplicated by content hash.
- Project-specific instruction files were kept because they are useful templates for future adaptation across Copilot, Codex, Claude, Kimi, and Cursor.
- Sensitive local secrets were intentionally excluded from this repository.
- Full provenance is documented in `docs/SOURCES.md`.
