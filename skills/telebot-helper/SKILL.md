---
name: telebot-helper
description: Reviews and helps implement Telebot (gopkg.in/telebot.v4) handlers, YAML layouts, callbacks, and bot structure. Use when working with Telegram bot code.
allowed-tools: Read, Grep, Glob, Bash, Edit, Write
---

# Telebot Review & Helper

Review and assist with Telebot framework code. Enforce these mandatory conventions.

## Critical Rule

**NEVER hardcode bot content in Go code.** ALL buttons, markups, and messages MUST be in YAML files (`bot.yml`, `locales/*.yml`). Use `b.lt.Markup()`, `b.lt.Text()`, `b.lt.Button()` to reference them.

Exception: Create markups programmatically only when YAML is technically impossible.

## Layout Package

### Initialization (must be present)
```go
lt, err := layout.New("bot.yml")
b, err := tele.NewBot(lt.Settings())
b.Use(lt.Middleware("uk"))  // MANDATORY for lt.Text()/lt.Markup() to work
```

### Core Methods
```go
lt.Text(c, "key")                          // Auto locale from context
lt.Text(c, "key", templateData)            // With template data
lt.TextLocale("uk", "key")                 // Explicit locale
lt.Markup(c, "markup_name")               // Auto locale
lt.MarkupLocale("uk", "name", data)       // Explicit locale + data
lt.Button(c, "button_name")              // Single button
b.Handle(lt.Callback("button_name"), h)  // Use YAML key, NOT unique field
```

Grouped locales use dot notation: `lt.Text(c, "meeting.joining")`

## bot.yml Structure
```yaml
buttons:
  button_name:             # ← Use THIS name in lt.Callback()
    unique: callback_id    # Callback identifier (can be shared)
    text: "Text"
    data: "arg1|arg2"      # Pipe-separated args

markups:
  markup_name:
    - [btn1, btn2]         # Inline row
```

## Callback Handling

- Register with button NAME (YAML key), not `unique` field
- Use `c.Args()` for arguments (auto-split by `|`), NEVER parse `c.Callback().Data` manually
- Consolidate related buttons: share `unique`, differentiate by `data`

```yaml
yes_btn:
  unique: did_meet
  data: "yes|{{ .MatchID }}"
no_btn:
  unique: did_meet
  data: "no|{{ .MatchID }}"
```

```go
func (b *Bot) onDidMeet(c tele.Context) error {
    args := c.Args() // ["yes", "123"] or ["no", "123"]
    answer := args[0]
    matchID, _ := strconv.ParseInt(args[1], 10, 64)
    // ...
}
```

## Error Handling

1. Don't log before returning (global `onError` logs all)
2. Wrap service/business errors with context via `fmt.Errorf`
3. Return telebot errors (`c.Send`, `c.Edit`, `c.Delete`, `b.Send`) as-is — no wrapping
4. Use `slog.Debug()` for non-critical background tasks

## Handler Style

- Inline `lt.Text()`/`lt.Markup()` in send calls — don't assign to variables
- Use `c.Send()` for current user, `b.Bot().Send(tele.ChatID(id), ...)` for other users
- Handler method names: `on*` prefix (`onStart`, `onRate`, `onReady`)

## Config Access
```go
b.lt.String("api_url")       // string
b.lt.Int("max_retries")      // int
b.lt.Duration("timeout")     // time.Duration
b.lt.Strings("features")     // []string
b.lt.Int64s("admin_ids")     // []int64
```

## Process

1. Read the bot handler files, `bot.yml`, and `locales/*.yml`
2. Check all rules above
3. Flag hardcoded content, wrong callback registration, improper error handling
4. Suggest corrected code
