# Building a Design System with CSS Variables

> CSS custom properties changed how we think about theming. Here's how I structured a design system that scales from a single component to an entire product.

![Design system token hierarchy](https://picsum.photos/seed/design-system/1200/630)

## The Problem

Every project I touched had the same issue: colors, spacing, and typography were scattered across dozens of files. Change a brand color and you'd hunt through 40+ files.

I wanted a single source of truth that:

- Works without a build step
- Supports dark mode with zero JavaScript
- Lets components override tokens locally
- Stays readable without a linter

## The Token Hierarchy

I settled on three levels:

| Level | Scope | Example |
|-------|-------|---------|
| **Primitive** | Global | `--blue-500: #3b82f6` |
| **Semantic** | Global | `--color-primary: var(--blue-500)` |
| **Component** | Local | `--btn-bg: var(--color-primary)` |

### Primitives

These are raw values. No meaning, just a palette:

```css
:root {
  --gray-50: #f9fafb;
  --gray-100: #f3f4f6;
  --gray-200: #e5e7eb;
  --gray-900: #111827;
  --blue-500: #3b82f6;
  --blue-600: #2563eb;
}
```

### Semantic Tokens

This is where the magic happens. Components never reference `--blue-500` directly — they reference *intent*:

```css
:root {
  --color-primary: var(--blue-500);
  --color-primary-hover: var(--blue-600);
  --color-surface: var(--gray-50);
  --color-text: var(--gray-900);
  --color-text-muted: var(--gray-500);
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-surface: var(--gray-900);
    --color-text: var(--gray-50);
    --color-text-muted: var(--gray-400);
  }
}
```

Dark mode is just a media query flipping semantic tokens. No JS, no class toggling.

### Component Tokens

Each component defines its own local scope:

```css
.button {
  --btn-bg: var(--color-primary);
  --btn-text: white;
  --btn-radius: 0.5rem;

  background: var(--btn-bg);
  color: var(--btn-text);
  border-radius: var(--btn-radius);
}

.button--danger {
  --btn-bg: var(--red-500);
}
```

Override one variable, the whole component re-themes.

## Spacing Scale

I use a **rem-based** scale (per project convention) with a 4px base:

```css
:root {
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-6: 1.5rem;
  --space-8: 2rem;
  --space-12: 3rem;
}
```

> **Rule of thumb:** if you need a spacing value that isn't in the scale, you probably need a new component boundary.

## Typography

```css
:root {
  --font-sans: "Geist", system-ui, sans-serif;
  --font-mono: "Geist Mono", monospace;
  --font-serif: "Instrument Serif", serif;

  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
  --text-2xl: 1.5rem;
  --text-3xl: 1.875rem;
}
```

## Putting It Together

A card component using the full hierarchy:

```css
.card {
  --card-bg: var(--color-surface);
  --card-border: var(--border);
  --card-padding: var(--space-4);
  --card-radius: 0.75rem;

  background: var(--card-bg);
  border: 1px solid var(--card-border);
  padding: var(--card-padding);
  border-radius: var(--card-radius);
}
```

## What I'd Do Differently

1. **Add a lint rule** — enforce that components only reference semantic tokens, never primitives
2. **Generate docs** — a small script that reads the CSS and outputs a token reference page
3. **Version tokens** — when you rename `--color-primary` to `--color-accent`, you want a migration path

## Resources

- [MDN: CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
- [Open Design System Tokens](https://design-tokens.github.io/community-group/format/)
- [Tailwind's CSS variable approach](https://tailwindcss.com/docs/upgrade-guide#css-variables)

---

*Next up: [A Practical Guide to Server Components](/blog/practical-server-components)*
