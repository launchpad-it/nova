# nova

Claude Code plugin for fullstack development with Go, React/HTML, and Telebot.

## What's included

| Command | Description |
|---|---|
| `/nova:commit` | Smart multi-commit workflow — groups changes by scope |
| `/nova:go-review` | Go code review: style, context management, anti-patterns |
| `/nova:react-review` | React/HTML/TS review: hooks, accessibility, security |
| `/nova:telebot-helper` | Telebot handler review: YAML layouts, callbacks, error handling |
| `/nova:security-review` | Fullstack security audit (OWASP top 10, credentials, injections) |

## Install

```bash
claude /plugin marketplace add launchpad-it/nova
claude /plugin install nova
```

## Test locally

```bash
claude --plugin-dir /path/to/nova
```

## Structure

```
nova/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── commit.md
└── skills/
    ├── go-review/SKILL.md
    ├── react-review/SKILL.md
    ├── telebot-helper/SKILL.md
    └── security-review/SKILL.md
```
