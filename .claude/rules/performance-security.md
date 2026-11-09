---
paths: ["src/routes/**", "src/api/**", "src/services/**"]
---

# Performance & Security Rules

## Performance
- No N+1 queries: always use eager loading or batch queries
- Paginate all list endpoints (default: 20, max: 100)
- Cache expensive computations where possible

## Security
- Parameterized queries only (never string concatenation for SQL)
- Sanitize all user input before rendering (XSS protection)
- Never expose stack traces in API responses (use generic 500 message)
- Rate-limit authentication endpoints
