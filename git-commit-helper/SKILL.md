---
name: git-commit-helper
description: Provides expert guidance for Git commit messages, including conventional commit formatting and best practices. Use when creating or editing Git commit messages.
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Overview

Provides expert guidance for creating conventional commit messages that follow industry best practices. Helps generate clear, consistent commit messages by analyzing staged changes.

# Conventional Commit Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

## Commit Types

- **feat**: A new feature for the user
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Changes that don't affect code meaning (white-space, formatting, missing semi-colons, etc.)
- **refactor**: Code change that neither fixes a bug nor adds a feature
- **perf**: Code change that improves performance
- **test**: Adding missing tests or correcting existing tests
- **build**: Changes that affect the build system or external dependencies
- **ci**: Changes to CI configuration files and scripts
- **chore**: Other changes that don't modify src or test files
- **revert**: Reverts a previous commit

## Rules

1. **Type is mandatory**: Choose the most appropriate type from the list above
2. **Scope is optional**: Add in parentheses to specify what part of codebase (e.g., `feat(auth):`, `fix(api):`)
3. **Description**:
   - Use imperative mood ("add" not "added" or "adds")
   - Don't capitalize first letter
   - No period at the end
   - Keep under 72 characters
4. **Body is optional**: Provide context about what and why, not how
5. **Footer is optional**: Reference issues, note breaking changes

## Examples

### Simple commit
```
feat: add user authentication
```

### With scope
```
fix(auth): prevent token expiration on refresh
```

### With body
```
refactor(api): restructure endpoint handlers

Move handler logic into separate service layer to improve
testability and maintain single responsibility principle.
```

### Breaking change
```
feat(api): change authentication response format

BREAKING CHANGE: API now returns user object instead of just token.
Update all API consumers to handle new response structure.
```

### Multiple footers
```
fix(database): resolve connection pool exhaustion

Fixes #123
Closes #456
```
