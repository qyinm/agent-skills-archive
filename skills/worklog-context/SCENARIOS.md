# Worklog Context Usage Scenarios

This file contains detailed usage scenarios and examples for the worklog-context skill.

## Scenario 1: Starting New Feature

**Situation**: You need to add payment processing to the application.

**User Request**: "I need to add payment processing"

**Skill Action**:
```
Assistant: "Let me check our worklog history to see if there's any related work."
[Runs /worklog-context --topic "payment OR billing OR stripe"]
```

**Analysis Result**:
- No direct payment work found
- Found similar API integration patterns used for other services
- Identified files: `src/services/api-client.ts` (modified 8 times for integrations)
- Pattern: All external APIs use service wrapper + error handling + retry logic

**Output Summary**:
```markdown
# Worklog Context Analysis

**Search Criteria**: payment OR billing OR stripe
**Date Range**: 2025-12-10 to 2026-01-09
**Entries Found**: 0 direct matches, 3 related API integration patterns

---

## Related Past Work

### 2026-01-05 - feat: Add SendGrid email integration
- Integrated external API using service wrapper pattern
- Implemented retry logic for failed requests
- Files: `src/services/sendgrid.service.ts`

### 2025-12-18 - feat: Add Slack notification integration
- Similar external API pattern
- Used environment-based configuration
- Files: `src/services/slack.service.ts`, `src/config/integrations.ts`

---

## Frequently Modified Files

1. `src/services/api-client.ts` (modified 8 times)
2. `src/config/integrations.ts` (modified 5 times)

---

## Patterns & Insights

- All external APIs use a consistent service wrapper pattern
- Configuration stored in `src/config/integrations.ts`
- Retry logic with exponential backoff is standard
- Error handling follows custom `ApiError` class pattern

---

## Recommendations for Current Work

1. Review `src/services/api-client.ts` for the base API integration pattern
2. Follow established pattern: create `src/services/payment.service.ts`
3. Add payment provider config to `src/config/integrations.ts`
4. Implement retry logic similar to SendGrid integration
5. Use `ApiError` class for consistent error handling
```

**Assistant Response**: "Based on past work, we've integrated external APIs like SendGrid and Slack using a consistent service wrapper pattern. I recommend following the same approach for payment processing..."

---

## Scenario 2: Debugging

**Situation**: Database keeps timing out in production.

**User Request**: "The database keeps timing out"

**Skill Action**:
```
Assistant: "Let me see if we've dealt with database issues before."
[Runs /worklog-context --topic "database OR timeout OR connection"]
```

**Analysis Result**:
- Found 2 related entries from past 30 days
- Previous timeout issue resolved on 2026-01-08
- Solution involved connection pool configuration

**Output Summary**:
```markdown
# Worklog Context Analysis

**Search Criteria**: database OR timeout OR connection
**Date Range**: 2025-12-10 to 2026-01-09
**Entries Found**: 2

---

## Key Past Work

### 2026-01-08 - fix: Resolve database connection timeout
- **Problem**: Connection pool exhaustion under high load
- **Solution**: Increased pool size from 10 to 20, added retry logic
- **Files**: `src/config/database.config.ts`, `src/app.module.ts`
- **Notes**: Added monitoring to track connection usage

### 2025-12-20 - perf: Optimize database query performance
- Added connection pooling
- Implemented query result caching
- **Files**: `src/config/database.config.ts`

---

## Frequently Modified Files

1. `src/config/database.config.ts` (modified 3 times)

---

## Patterns & Insights

- Connection pool size matters significantly under load
- Previous exhaustion occurred during peak traffic
- Monitoring added via custom metrics endpoint
- Retry logic essential for transient failures

---

## Recommendations for Current Work

1. Check current connection pool size in `src/config/database.config.ts`
2. Review metrics endpoint to see if pool is exhausted again
3. Consider if recent traffic increase requires larger pool
4. Verify retry logic is working properly
5. May need to increase pool size beyond 20 if traffic increased significantly
```

**Assistant Response**: "I found that we fixed a similar issue on 2026-01-08. The solution was to increase the connection pool size and add retry logic. Let me check your current configuration..."

---

## Scenario 3: Understanding Codebase

**Situation**: New team member wants to understand authentication.

**User Request**: "How does authentication work in this project?"

**Skill Action**:
```
Assistant: "Let me review our authentication work history."
[Runs /worklog-context --topic authentication]
```

**Analysis Result**:
- Authentication implemented over 3 iterations
- Evolution tracked from initial JWT to current refresh token system

**Output Summary**:
```markdown
# Worklog Context Analysis

**Search Criteria**: authentication
**Date Range**: 2025-11-10 to 2026-01-09
**Entries Found**: 4

---

## Key Past Work (Chronological Evolution)

### 2025-11-15 - feat: Add user authentication system
- **Initial implementation**: JWT-based authentication
- Added login and registration endpoints
- Password hashing with bcrypt
- **Files**: `src/auth/auth.service.ts`, `src/auth/auth.controller.ts`
- **Decision**: JWT over sessions for stateless authentication

### 2025-11-22 - feat: Add authentication middleware
- Implemented route protection
- Token validation on protected routes
- **Files**: `src/middleware/auth.middleware.ts`

### 2025-12-05 - feat: Add refresh token mechanism
- Implemented refresh tokens for security
- Token expiry: access 15min, refresh 7 days
- **Files**: `src/auth/auth.service.ts`, `src/auth/refresh-token.service.ts`

### 2026-01-09 - fix: Resolve CORS issues in API
- CORS preventing authenticated requests
- Added proper CORS configuration
- **Files**: `src/main.ts`, `src/config/cors.config.ts`

---

## Frequently Modified Files

1. `src/auth/auth.service.ts` (modified 5 times)
2. `src/auth/auth.controller.ts` (modified 3 times)
3. `src/middleware/auth.middleware.ts` (modified 2 times)

---

## Patterns & Insights

- **Architecture**: JWT-based stateless authentication
- **Security**: Refresh tokens implemented for better security
- **Token lifetime**: Access tokens 15min, refresh tokens 7 days
- **Password handling**: bcrypt with appropriate salt rounds
- **Known issues**: CORS must be properly configured for auth to work

---

## Recommendations

1. Start with `src/auth/auth.service.ts` - core authentication logic
2. Review `src/middleware/auth.middleware.ts` to understand route protection
3. Check `src/auth/refresh-token.service.ts` for token refresh flow
4. Note: CORS configuration in `src/config/cors.config.ts` is critical
5. Token expiry times can be adjusted in auth.service.ts if needed
```

**Assistant Response**: "Here's how authentication evolved in this project: Started with basic JWT (Nov 15), added middleware for route protection (Nov 22), then implemented refresh tokens for better security (Dec 5)..."

---

## Scenario 4: Avoiding Past Mistakes

**Situation**: Planning to implement caching.

**User Request**: "I want to add caching to improve performance"

**Skill Action**:
```
Assistant: "Let me check if we've tried caching before and what we learned."
[Runs /worklog-context --topic "cache OR caching OR performance"]
```

**Analysis Result**:
- Caching was attempted before but rolled back
- Important lessons learned documented

**Output Summary**:
```markdown
# Worklog Context Analysis

**Search Criteria**: cache OR caching OR performance
**Date Range**: 2025-12-10 to 2026-01-09
**Entries Found**: 3

---

## Key Past Work

### 2025-12-28 - revert: Remove Redis caching layer
- **Background**: Redis caching added on 2025-12-15
- **Problem**: Cache invalidation bugs caused stale data
- **Decision**: Rolled back, need better invalidation strategy
- **Files**: Removed `src/cache/redis.service.ts`
- **Notes**: "Don't add caching again without solving invalidation"

### 2025-12-20 - perf: Optimize database query performance
- Added query result caching at database level
- This worked well - simpler than Redis
- **Files**: `src/config/database.config.ts`

### 2025-12-15 - feat: Add Redis caching layer (REVERTED)
- Initial attempt at caching
- **Files**: `src/cache/redis.service.ts`

---

## Patterns & Insights

- ⚠️ **Critical Warning**: Previous Redis caching attempt failed due to cache invalidation issues
- Database-level query caching worked well (simpler approach)
- Cache invalidation was the main challenge
- Team decided simpler is better for this use case

---

## Recommendations for Current Work

1. ⚠️ **Read the notes from 2025-12-28 carefully** - cache invalidation must be solved first
2. Consider if database-level caching is sufficient (it worked well)
3. If adding application-level caching, plan invalidation strategy upfront
4. Review `src/config/database.config.ts` to see current query caching
5. Don't repeat the Redis mistake without addressing invalidation
```

**Assistant Response**: "⚠️ Important: We tried adding Redis caching before but had to roll it back due to cache invalidation bugs. The team documented this on Dec 28. Database-level query caching is working well. Before adding application-level caching, we need a solid invalidation strategy..."

---

## Scenario 5: Finding Related Refactors

**Situation**: Considering refactoring API endpoints.

**User Request**: "I'm thinking of refactoring the API endpoints structure"

**Skill Action**:
```
Assistant: "Let me see what API-related refactoring we've done before."
[Runs /worklog-context --topic API --category refactor]
```

**Analysis Result**:
- Two previous API refactors provide useful patterns

**Output Summary**:
```markdown
# Worklog Context Analysis

**Search Criteria**: API (category: refactor)
**Date Range**: 2025-12-10 to 2026-01-09
**Entries Found**: 2

---

## Key Past Work

### 2026-01-03 - refactor: Restructure endpoint handlers
- **Change**: Moved handler logic into service layer
- **Reason**: Improve testability, maintain single responsibility
- **Pattern**: Controllers handle HTTP, services handle business logic
- **Files**: `src/api/`, `src/services/`
- **Result**: Much easier to test, cleaner code

### 2025-12-10 - refactor: Standardize API response format
- **Change**: All endpoints return consistent response structure
- **Pattern**: `{ success, data, error }` structure
- **Files**: `src/common/response.dto.ts`, all controllers
- **Result**: Frontend integration much simpler

---

## Patterns & Insights

- Team prefers service layer pattern (controller → service → repository)
- Consistent response format is highly valued
- Previous refactors improved testability significantly
- Migration was gradual (endpoint by endpoint, not all at once)

---

## Recommendations for Current Work

1. Review `src/services/` to see established service layer pattern
2. Check `src/common/response.dto.ts` for standard response format
3. Consider gradual migration like previous refactors
4. Maintain controller → service → repository separation
5. Ensure new structure improves testability
```

**Assistant Response**: "Based on past refactors, the team successfully moved to a service layer pattern (Jan 3) and standardized response formats (Dec 10). Both improved testability and code clarity. I recommend following the same patterns..."

---

## Pro Tips for Using This Skill

### 1. Use Before Starting Work

Always check worklog context before beginning new features:
```bash
# Starting authentication work?
/worklog-context --topic authentication

# Adding a new API endpoint?
/worklog-context --topic API --recent

# Fixing a bug?
/worklog-context --topic <bug-area> --category fix
```

### 2. Combine Multiple Keywords

Use OR to cast a wider net:
```bash
/worklog-context --topic "auth OR login OR jwt OR session"
```

### 3. Check Related Files

If you know which file you'll work on:
```bash
/worklog-context --files src/auth/auth.service.ts
```

### 4. Look Back Further for Architectural Decisions

```bash
# Default is 30 days, but architectural decisions might be older
/worklog-context --topic "database design" --days 90
```

### 5. Review Weekly

Get a summary every Monday:
```bash
/worklog-context --recent  # Last 7 days
```

### 6. Before Proposing Solutions

Check if similar approaches were tried:
```bash
# "Let's add Redis caching!"
# First check:
/worklog-context --topic "cache OR redis"
# (Might find it was already tried and reverted!)
```

### 7. Natural Language Works

You don't need to memorize flags:
```
"What have we done with the payment system?"
"Show me recent database work"
"Has anyone fixed CORS issues before?"
```

Claude will automatically translate these into appropriate worklog context searches.

---

## Integration with Development Workflow

### Morning Routine
```bash
# See what the team did recently
/worklog-context --recent
```

### Before New Feature
```bash
# Check related past work
/worklog-context --topic <feature-name>
```

### During Debugging
```bash
# Check if this bug was fixed before
/worklog-context --topic <error-keywords> --category fix
```

### Code Review Time
```bash
# Understand why patterns were chosen
/worklog-context --files <changed-files>
```

### Architecture Planning
```bash
# Review past architectural decisions
/worklog-context --topic <architecture-area> --days 180
```
