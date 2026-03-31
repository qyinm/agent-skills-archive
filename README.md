# Agent Skills Archive

Reusable agent skills for AI coding assistants. Compatible with Claude Code,
Codex, Cursor, OpenCode, and 40+ agents via [skills.sh](https://skills.sh).

## Quick Install

```bash
# Install all skills
npx skills add qyinm/agent-skills-archive

# Preview available skills
npx skills add qyinm/agent-skills-archive --list

# Install specific skill
npx skills add qyinm/agent-skills-archive --skill career-management

# Global install (all projects)
npx skills add qyinm/agent-skills-archive -g
```

## Available Skills

| Skill | Description |
|-------|-------------|
| [career-management](skills/career-management/) | Resume versioning, job application pipeline, interview prep |
| [git-commit-helper](skills/git-commit-helper/) | Conventional commit message guidance |
| [ios-testflight-release](skills/ios-testflight-release/) | iOS app archive → TestFlight upload via xcodebuild |
| [log-work](skills/log-work/) | Auto-log development work into date-based changelog files |
| [rubberduck-learning](skills/rubberduck-learning/) | Learning-first debugging with Socratic questioning |
| [worklog-context](skills/worklog-context/) | Auto-search past worklogs for task context |
| [agent-skill-creator](skills/agent-skill-creator/) | Generate new agent skills |

## Skill Structure

```
skills/
├── <skill-name>/
│   ├── SKILL.md          # Required: instructions + frontmatter
│   ├── EXAMPLES.md       # Optional: usage examples
│   ├── SCENARIOS.md      # Optional: detailed scenarios
│   ├── scripts/          # Optional: helper scripts
│   ├── references/       # Optional: reference docs
│   └── assets/           # Optional: images, templates
```

## Naming Convention

- lowercase, hyphens only (`career-management`)
- short, descriptive, action-oriented
