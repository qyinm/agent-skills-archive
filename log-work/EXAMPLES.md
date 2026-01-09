# Work Log Examples

This file contains detailed examples of work log entries and file structures.

## Example Output Files

### File: `docs/worklog/2026-01-09.md`

```markdown
# Work Log - 2026-01-09

---

## [14:30] feat: Add user authentication system

### Changes
- Implemented JWT-based authentication
- Added login and registration endpoints
- Created user model and database schema
- Added password hashing with bcrypt
- Implemented authentication middleware

### Files Modified
- `src/auth/auth.service.ts`
- `src/auth/auth.controller.ts`
- `src/user/user.model.ts`
- `src/middleware/auth.middleware.ts`
- `package.json`

### Notes
Chose JWT over sessions for stateless authentication. Token expiry set to 24 hours. Refresh token mechanism to be implemented in next iteration.

---

## [10:15] fix: Resolve CORS issues in API

### Changes
- Added CORS middleware configuration
- Whitelisted frontend domain
- Configured credentials support

### Files Modified
- `src/main.ts`
- `src/config/cors.config.ts`

### Notes
Frontend was unable to make authenticated requests due to CORS policy. Now properly configured for development and production environments.

---
```

### File: `docs/worklog/2026-01-08.md`

```markdown
# Work Log - 2026-01-08

---

## [16:45] fix: Resolve database connection timeout

### Changes
- Increased connection pool size to 20
- Added retry logic for failed connections
- Implemented connection health checks

### Files Modified
- `src/config/database.config.ts`
- `src/app.module.ts`

### Notes
Issue was caused by connection pool exhaustion under high load. Monitoring added to track connection usage.

---
```

## Example Usage Scenarios

### Scenario 1: Basic usage after completing a feature

```bash
# You just finished implementing a new feature
/log-work
```

Claude will:
1. Check `git diff` and `git status`
2. Analyze the changes
3. Create an entry in `docs/worklog/2026-01-09.md`
4. Show you what was logged

### Scenario 2: Custom title for specific work

```bash
# You want to specify exactly what you did
/log-work --title "Refactor authentication module for better maintainability"
```

Claude will create an entry with your custom title instead of inferring from git.

### Scenario 3: Categorize work explicitly

```bash
# You fixed a critical bug
/log-work --category fix --title "Fix memory leak in WebSocket handler"
```

Claude will create an entry with the "fix" category prominently displayed.

### Scenario 4: Retroactive logging

```bash
# You forgot to log yesterday's work
/log-work --date 2026-01-08 --title "Implemented email notification system"
```

Claude will create or append to yesterday's worklog file.

### Scenario 5: Manual entry without git analysis

```bash
# You had a meeting or did non-code work
/log-work --skip-git --title "Team meeting: Architecture decisions for Q1"
```

Claude will create an entry based on your description without analyzing git changes.

## Multiple Entries Same Day

When you run `/log-work` multiple times in one day, entries are added to the same file:

```markdown
# Work Log - 2026-01-09

---

## [16:00] feat: Add email notifications

### Changes
- Integrated SendGrid API
- Created email templates
- Added notification service

### Files Modified
- `src/notifications/email.service.ts`
- `src/templates/welcome-email.html`

### Notes
Using SendGrid for transactional emails. Templates use Handlebars for dynamic content.

---

## [14:30] feat: Add user authentication system

### Changes
- Implemented JWT-based authentication
- Added login and registration endpoints

### Files Modified
- `src/auth/auth.service.ts`

### Notes
JWT chosen for stateless authentication.

---

## [10:15] fix: Resolve CORS issues in API

### Changes
- Added CORS middleware configuration

### Files Modified
- `src/main.ts`

### Notes
CORS properly configured for development and production.

---
```

Notice: Newest entries appear at the top (reverse chronological order).

## Categories Reference

| Category | Use When |
|----------|----------|
| **feat** | Adding new features or capabilities |
| **fix** | Fixing bugs or issues |
| **refactor** | Restructuring code without changing behavior |
| **docs** | Updating documentation, comments, or README |
| **style** | Code formatting, linting, or style improvements |
| **test** | Adding or updating tests |
| **chore** | Maintenance tasks, dependency updates |
| **perf** | Performance improvements |

## Tips for Quality Entries

1. **Be specific in Changes**: Don't just say "updated auth" - say "Added JWT token refresh logic to handle expired tokens"

2. **Include reasoning in Notes**: Future you will thank you for explaining why decisions were made

3. **List all relevant files**: Don't skip files even if changes were small - it helps track which parts of codebase are actively changing

4. **Group related work**: If you made several small commits for one logical feature, group them in one worklog entry

5. **Use consistent terminology**: If you call it "authentication" in one entry, don't call it "auth" or "login system" in another
