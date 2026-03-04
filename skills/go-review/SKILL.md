---
name: go-review
description: Reviews Go code for style violations, anti-patterns, and convention adherence. Use when writing, reviewing, or modifying Go code.
allowed-tools: Read, Grep, Glob, Bash
---

# Go Code Review

Review Go code against these mandatory conventions. Flag every violation.

## Critical Rules

### Never ignore function parameters
If a function accepts a parameter, it must use it. Otherwise fix the signature.

```go
// WRONG
func (s *Service) Process(_ context.Context, data Data) error {
    return s.doWork(data)
}

// CORRECT
func (s *Service) Process(data Data) error {
    return s.doWork(data)
}
```

### Never return nil result and nil error
Functions with signature `(T, error)` must guarantee: if `err == nil`, the result is valid.

```go
// WRONG
if resp.StatusCode == http.StatusNoContent {
    return nil, nil  // BUG: silent failure
}

// CORRECT
if resp.StatusCode == http.StatusNoContent {
    return nil, ErrContactNotFound
}
```

## Style

- Group multiple vars in a `var` block (not separate `:=` declarations)
- Use `any` instead of `interface{}`
- Break lines >100 chars for `slog.*`, `fmt.Sprintf`, etc.
- Prefer zero values over `sql.Null*` types (`NOT NULL DEFAULT` + zero values)
- Exception: `*time.Time` for optional timestamps

## Modern Go (1.21+)

- Use `min()`, `max()`, `clear()` builtins
- Use `for range n` and `for i := range 5` (not `for i := 0; i < n; i++`)
- Use `slices` and `maps` packages: `slices.Backward`, `slices.Collect`, `slices.Sorted`, `maps.Keys`, `maps.Values`

## Context Management

| Situation | Context |
|-----------|---------|
| Webhook handler | `context.Background()` |
| Goroutine outliving caller | `context.Background()` |
| Event bus / pub-sub handler | `context.Background()` |
| Scheduled/cron job | `context.Background()` |
| User-facing HTTP handler | `c.Request().Context()` |
| Synchronous sub-call | Pass parent `ctx` |

Rules:
1. Goroutines that outlive the caller get `context.Background()`
2. Webhook handlers must use `context.Background()`
3. Event bus handlers always get `context.Background()`
4. Pass context as argument, never store in structs
5. Unused context = wrong signature — remove it

## Development

- **Startup validation**: Critical dependencies (DB, APIs, config) checked at startup with `log.Fatal()`, never in handlers
- **Optional features**: Gracefully degrade in handlers
- **Loop efficiency**: Break immediately when searching for a single item

## Process

1. Read the changed/target `.go` files
2. Check each rule above
3. Report violations with file:line and the rule violated
4. Suggest corrected code for each violation
