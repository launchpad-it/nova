---
name: security-review
description: Performs security audit across Go backend and React/HTML frontend code. Checks for OWASP top 10, injection risks, credential exposure, and insecure patterns.
allowed-tools: Read, Grep, Glob
---

# Fullstack Security Review

Audit the codebase for security vulnerabilities across the full stack (Go + React/HTML).

## Backend (Go)

### Injection
- SQL injection: raw string concatenation in queries, missing parameterized queries
- Command injection: `os/exec` with unsanitized user input
- Path traversal: user input in file paths without sanitization
- LDAP/NoSQL injection where applicable

### Authentication & Authorization
- Hardcoded credentials, API keys, tokens in source code
- Secrets in logs (`slog`, `fmt`, `log`)
- Missing auth checks on protected endpoints
- Weak session management

### Data Exposure
- Sensitive data in error messages returned to clients
- Overly permissive CORS configuration
- Missing rate limiting on sensitive endpoints
- PII logged or exposed in responses

### Crypto
- Weak hashing (MD5, SHA1 for security purposes)
- Hardcoded encryption keys
- Insecure random number generation (`math/rand` instead of `crypto/rand`)

### Context & Concurrency
- Race conditions in shared state
- Missing mutex/sync for concurrent map access
- Goroutine leaks (missing context cancellation)

## Frontend (React/HTML)

### XSS
- `dangerouslySetInnerHTML` without sanitization
- User input rendered without escaping
- `javascript:` URLs in `href`
- Inline event handlers with user data

### Data Handling
- Sensitive data in localStorage/sessionStorage
- API keys or secrets in client-side code
- Credentials in URL parameters
- Missing CSRF protection on forms

### Dependencies
- Known vulnerable packages (check `go.sum`, `package-lock.json`)
- Outdated dependencies with security patches available

## Process

1. Search for patterns: `Grep` for credentials, secrets, unsafe functions
2. Read flagged files for context
3. Classify findings by severity: Critical / High / Medium / Low
4. Report each finding with:
   - File and line number
   - Vulnerability type
   - Risk description
   - Remediation suggestion
