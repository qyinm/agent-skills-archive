# Agent Skills Archive

Model-agnostic skill archive for reusable agent workflows.

This repository stores filesystem-based skills that can be reused across coding assistants.
Each skill is a folder with a `SKILL.md` file, and may include optional `scripts/`, `references/`, and `assets/`.

## Repository Goal

- Keep skills portable across tools (Codex, Claude Code, and others)
- Keep `SKILL.md` frontmatter minimal: `name`, `description`
- Keep instructions focused, actionable, and easy to maintain

## Current Skills

- [`git-commit-helper`](./git-commit-helper/)
- [`log-work`](./log-work/)
- [`worklog-context`](./worklog-context/)
- [`rubberduck-learning`](./rubberduck-learning/)

## Skill Structure

```text
<skill-name>/
├── SKILL.md
├── scripts/        (optional)
├── references/     (optional)
└── assets/         (optional)
```

## Usage Notes

- Use explicit invocation when available (for example, `$rubberduck-learning`)
- Also rely on implicit invocation via clear `description` triggers
- Keep vendor-specific metadata separate and optional

## Naming Convention

- lowercase letters, numbers, hyphens only
- short, descriptive, action-oriented names

## License

Add a license file if you plan to share this repository publicly.
