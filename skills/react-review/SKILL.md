---
name: react-review
description: Reviews React/HTML/CSS/TypeScript code for best practices, accessibility, and common pitfalls. Use when writing, reviewing, or modifying frontend code.
allowed-tools: Read, Grep, Glob, Bash
---

# React & HTML Code Review

Review frontend code against these conventions. Flag every violation.

## React Rules

### Component Structure
- Prefer functional components with hooks
- Extract custom hooks when logic is reused across components
- Keep components focused — one responsibility per component
- Colocate related files (component, styles, tests, types)

### State Management
- Use `useState` for local UI state
- Lift state only when siblings need it
- Avoid prop drilling beyond 2 levels — use context or composition
- Never mutate state directly — always return new references

### Hooks
- Follow Rules of Hooks (top-level only, React functions only)
- Include all dependencies in `useEffect`/`useMemo`/`useCallback` dep arrays
- Clean up side effects in `useEffect` return
- Don't overuse `useMemo`/`useCallback` — only when there's a measurable perf issue

### Event Handlers
- Name handlers with `handle` prefix: `handleClick`, `handleSubmit`
- Props callbacks with `on` prefix: `onClick`, `onSubmit`

### Rendering
- Always provide a stable `key` for list items (never use array index for dynamic lists)
- Avoid inline object/array literals in JSX props (causes unnecessary re-renders)
- Use fragments (`<>...</>`) instead of wrapper divs when no DOM node is needed
- Conditional rendering: prefer early returns over nested ternaries

## TypeScript Rules

- Define prop types with `interface` (not `type` for component props)
- Avoid `any` — use `unknown` and narrow, or define proper types
- Use discriminated unions for state machines / variant types
- Export types alongside their components

## HTML & Accessibility

- Use semantic elements (`<nav>`, `<main>`, `<article>`, `<button>`) over generic `<div>`/`<span>`
- Every `<img>` needs `alt` text (decorative images: `alt=""`)
- Interactive elements must be keyboard-accessible
- Form inputs need associated `<label>` elements
- Use ARIA attributes only when semantic HTML is insufficient

## CSS

- Prefer CSS modules or scoped styles over global CSS
- Use relative units (`rem`, `em`) over `px` for font sizes
- Avoid `!important` — fix specificity instead

## Security

- Never use `dangerouslySetInnerHTML` without sanitization
- Validate and sanitize user input before rendering
- Don't interpolate user input into URLs without encoding

## Process

1. Read the changed/target frontend files
2. Check each rule above
3. Report violations with file:line and the rule violated
4. Suggest corrected code for each violation
