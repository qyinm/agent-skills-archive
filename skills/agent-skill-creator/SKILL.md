---
name: agent-skill-generator
description: Generate skills for Claude AI agents.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Overview
This skill allows you to generate skills for Claude AI agents. It provides a set of tools to create, edit, and manage skills.

# Usage
## Simple Usage
To use this skill, you can call the `generate_skill` function with the desired skill name and description. For example:

```bash
/generate_skill "my_skill" "This is my skill"
```

## Generate with user input

To generate a skill with user input, you can call the `generate_skill_with_user_input` function. For example:

```bash
/generate_skill_with_user_input {ARGUMENTS}
```


