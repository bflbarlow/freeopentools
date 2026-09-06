# Free Open Tools — Ecosystem Style Guide

**Version 1.0**

This document is the single source of truth for the look, feel, and behavior of every application in the Free Open Tools suite. Every tool — regardless of what it does — must feel like it belongs to the same family. A user should be able to jump from one tool to another and instantly know how to use it.

These are not suggestions. They are rules. Deviation requires a documented reason.

---

## 1. Core Principles

1. **Simplicity first.** If a feature, control, or piece of copy isn't essential, remove it. Every screen should have one obvious primary action.
2. **Zero learning curve.** A first-time user should understand the interface in under 5 seconds, with no onboarding, tutorial, or tooltip required.
3. **Speed is a feature.** No unnecessary animations, loaders, or blocking states. Tools should feel instant.
4. **Consistency over creativity.** Individual tools should not invent their own design language. Novelty is spent on *functionality*, not chrome.
5. **Accessible by default, not by request.** Accessibility is not a checklist added at the end — it is a base requirement of every component.
6. **No dark patterns.** No fake urgency, no forced sign-ups, no hidden costs, no manipulative UI. Ever.
7. **No emojis.** Emojis render differently across operating systems, browsers, and device models — they break consistency, accessibility, and internationalization. Use flat, vector-based iconography (SVG) instead. Every tool must ship its own icons or use a shared icon set — never rely on the user's device to render the right glyph.

---

## 2. Color System

### 2.1 Philosophy

The entire ecosystem is **black, white, and shades of gray**, with a **single blue accent** used sparingly and purposefully. Color is a tool for hierarchy and action — not decoration. If everything is colorful, nothing stands out.

### 2.2 The Palette

Define these as CSS custom properties on `:root` in every application, exactly as named below, so tools remain swappable and themeable from a single source.

```css
:root {
  /* Accent (same in both themes) */
  --color-accent:        #2563EB; /* primary blue — actions, links, focus */
  --color-accent-hover:  #1D4ED8; /* darker blue — hover/active states */
  --color-accent-soft:   #2563EB1A; /* 10% opacity accent — backgrounds, highlights */

  /* Semantic (used only when meaning must be unambiguous) */
  --color-success: #16A34A;
  --color-warning: #D97706;
  --color-danger:  #DC2626;
}

/* Light theme (default) */
:root,
[data-theme="light"] {
  --color-bg:            #FFFFFF;
  --color-bg-subtle:     #F5F5F7;
  --color-surface:       #FFFFFF;
  --color-border:        #E2E2E5;
  --color-text:          #111113;
  --color-text-muted:    #5B5B63;
  --color-text-disabled: #9A9AA1;
}

/* Dark theme */
[data-theme="dark"] {
  --color-bg:            #0E0E10;
  --color-bg-subtle:     #17171A;
  --color-surface:       #1C1C20;
  --color-border:        #2C2C32;
  --color-text:          #F2F2F3;
  --color-text-muted:    #A3A3AB;
  --color-text-disabled: #5B5B63;
}
```

### 2.3 Rules

- **Blue is the only accent color.** Do not introduce purple, teal, orange, or brand gradients. One blue, used consistently across all tools, is what makes the ecosystem recognizable.
- Blue is reserved for: primary actions, links, active/focused states, selection states, and progress indicators. It is never used for large background fills or decorative purposes.
- Semantic colors (success/warning/danger) are used **only** for their meaning (validation, alerts, destructive actions) — never as decoration or emphasis.
- Never rely on color alone to convey meaning (see Accessibility, §6.3).
- Pure black (`#000000`) and pure white (`#FFFFFF`) should be used sparingly as extremes — prefer the near-black/near-white values above, which reduce harsh contrast and eye strain.
- All color combinations used for text must meet WCAG AA contrast at minimum (see §6.2).

---

## 3. Icons

### 3.1 No Emojis — Ever

Emojis are **forbidden** across the entire ecosystem. The reasons are non-negotiable:

- **Inconsistent rendering.** The same emoji looks completely different on iOS, Android, Windows, macOS, and Linux. A wrench emoji on one device is a screwdriver on another.
- **Accessibility failures.** Screen readers announce emojis inconsistently — some read them as words, others as Unicode code points, others as nothing at all. They are not accessible.
- **Internationalization.** Emojis carry cultural context that doesn't translate. A "thumbs up" or "checkmark" emoji may be offensive or meaningless in some regions.
- **Brand inconsistency.** Emojis introduce personality and tone variance that conflicts with the flat, professional, minimal aesthetic of the ecosystem.

### 3.2 Flat Iconography

Use **flat, vector-based icons** (SVG) exclusively. Guidelines:

- **Style.** Flat, two-tone or single-color fills. No gradients, no drop shadows, no skeuomorphic detail. Icons should feel like they belong to the same set.
- **Stroke width.** Use a consistent stroke width across all icons in a given tool (recommend 1.5–2px at 24px size). Fill-based icons should have uniform density.
- **Size scale.** Icons are available in standard sizes: **16px, 20px, 24px, 32px**. Do not invent custom sizes — scale from these.
- **Color.** Icons use `var(--color-text)` for primary icons and `var(--color-text-muted)` for secondary/disabled icons. Accent color is used only for interactive states (hover, active, focus).
- **Accessibility.** Every icon that conveys meaning must have an accessible name via `aria-label` or a visually-hidden `<span>`. Decorative icons use `aria-hidden="true"`.
- **Source.** Ship icons as inline SVGs or use a shared icon font/ sprite. Do not load icons from external CDNs — the tools are serverless and must not depend on third-party resources.

### 3.3 Icon Recommendations

For a consistent, free, open-source icon set that works well at the ecosystem's scale, use one of:

- **Lucide** — clean, consistent, MIT-licensed, tree-shakeable SVG icons
- **Heroicons** (outline or solid) — clean, Tailwind-aligned, MIT-licensed
- **Phosphor** — flexible weight system, MIT-licensed

Pick **one** icon set for the entire ecosystem. Do not mix icon families.

---

## 4. Project Identifier

### 4.1 Formal Name

The suite is formally named **free open tools** — three words, lowercase, no hyphens or underscores. Use the full formal name in prose, headings, and any copy where the project is introduced or described.

### 4.2 Stylized Abbreviation

When space is tight or a compact identifier is needed, the project is stylized as a single run-on word:

**freeopentools**

with the following color treatment:

| Segment | Color Token |
|---|---|
| `free` | `--color-text` (light/dark theme text color) |
| `open` | `--color-accent` (`#2563EB`) |
| `tools` | `--color-text` (light/dark theme text color) |

```html
<div class="logo-text">free<span>open</span>tools</div>
```

```css
.logo-text {
  color: var(--color-text);
  font-weight: 700;
}
.logo-text span {
  color: var(--color-accent);
}
```

The middle segment (`open`) in blue creates a recognizable visual anchor that ties the identifier to the ecosystem's single accent color.

### 4.3 Usage Rules

| Context | Form | Example |
|---|---|---|
| Page title / heading | Formal | Free Open Tools |
| Navigation / logo | Stylized | free<span>open</span>tools |
| URL / domain | Compact | freeopentools.com |
| Social / brand references | Formal | Free Open Tools |
| Inline mentions in copy | Formal | "a collection of free open tools" |

- The stylized form (`freeopentools` with blue `open`) is reserved for the project's own UI — it is the mark, not the name. Do not use it as a prose substitute in paragraphs.
- In code, config, and file paths, use the all-lowercase compact form `freeopentools` (no color treatment).
- Never hyphenate or capitalize individual segments in the run-on form (e.g., not `FreeOpenTools`, `free-open-tools`, or `FREEOPENTOOLS`).

### 4.4 Etymology

The name reflects two core principles:
- **free** — no cost, no sign-up, no tracking, no gatekeeping
- **open** — source code is public, inspectable, forkable, improvable

The tools themselves are the artifact. The name puts the principles before the product.

---

## 5. Typography

### 5.1 Typeface

Use a single, highly legible system font stack across all applications — no custom/webfonts unless there is a strong functional reason (e.g., a monospace tool).

```css
--font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
             "Helvetica Neue", Arial, sans-serif;
--font-mono: "SF Mono", "SFMono-Regular", Consolas, "Liberation Mono",
             Menlo, monospace;
```

Using system fonts guarantees fast load times, native feel on every OS, and full Unicode/language support.

### 5.2 Scale

Use a constrained, predictable type scale. Do not invent new sizes per tool.

| Token | Size | Line Height | Use |
|---|---|---|---|
| `--text-xs` | 12px | 1.4 | Captions, metadata, tags |
| `--text-sm` | 14px | 1.5 | Secondary text, labels |
| `--text-base` | 16px | 1.6 | Body copy, inputs (minimum for body text) |
| `--text-lg` | 18px | 1.5 | Emphasized body, subheadings |
| `--text-xl` | 22px | 1.3 | Section headings |
| `--text-2xl` | 28px | 1.2 | Page titles |
| `--text-3xl` | 36px | 1.15 | Hero / landing headlines only |

Rules:
- **Never set body text below 16px.** Small text is an accessibility and readability failure, especially on mobile.
- Font weight range: 400 (body), 500 (labels/emphasis), 600–700 (headings only). Avoid weights below 400.
- Line length should stay between 45–75 characters for paragraph text (`max-width` on text blocks).
- Use `font-weight` and size for hierarchy — not color or italics.

---

## 6. Layout & Spacing

### 6.1 Spacing Scale

Use a single 4px-based spacing scale as CSS variables. All margins, padding, and gaps must use these tokens — no arbitrary pixel values.

```css
--space-1: 4px;
--space-2: 8px;
--space-3: 12px;
--space-4: 16px;
--space-5: 24px;
--space-6: 32px;
--space-7: 48px;
--space-8: 64px;
```

### 6.2 Structure

- **One primary action per screen.** Every tool has exactly one obvious "do the thing" button. It is styled distinctly (filled, accent color) and no other button on the page competes with it visually.
- Content is centered in a constrained container: `max-width: 720px` for text/tool-focused layouts, `max-width: 960px–1100px` for tools needing more workspace (e.g., editors, converters with side-by-side panels).
- Use generous whitespace over borders/dividers to separate sections. Borders are a last resort.
- Corner radius is consistent across the ecosystem:
  ```css
  --radius-sm: 6px;   /* inputs, small buttons, tags */
  --radius-md: 10px;  /* cards, panels */
  --radius-lg: 16px;  /* modals, large containers */
  ```
- Shadows are subtle and only used to indicate elevation (modals, dropdowns, floating tooltips) — never decoratively.
  ```css
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.06);
  --shadow-md: 0 4px 12px rgba(0,0,0,0.10);
  --shadow-lg: 0 12px 32px rgba(0,0,0,0.16);
  ```

---

## 7. Components

### 7.1 Buttons

Three variants only. Do not create additional button styles.

- **Primary** — solid accent background, white text. One per screen/section. Used for the main action.
- **Secondary** — transparent/outlined, border in `--color-border`, text in `--color-text`. Used for supporting actions.
- **Ghost/Text** — no background or border, accent-colored text. Used for low-emphasis actions (e.g., "Cancel", "Reset").

Rules:
- Minimum touch target: **44×44px** (Apple HIG / WCAG 2.5.5), even if the visible button is smaller — pad with invisible hit area.
- Minimum horizontal padding: `--space-4`; vertical: `--space-3`.
- Always show `:hover`, `:active`, `:focus-visible`, and `:disabled` states.
- Disabled buttons use `--color-text-disabled` and `cursor: not-allowed` — never simply lower opacity alone (insufficient contrast signal).
- Never use color alone to distinguish button importance — use weight/fill as well.

### 7.2 Forms & Inputs

- Every input has a **visible, persistent label** — never rely on placeholder text as a label (it disappears on input and fails accessibility).
- Inputs have a minimum height of 44px on touch devices.
- Font size in inputs is **16px minimum** to prevent mobile browsers (iOS Safari) from auto-zooming on focus.
- Validation errors appear inline, next to the field, with text + icon (not color alone), and are announced to screen readers via `aria-describedby` and `aria-invalid`.
- Use native HTML input types (`email`, `number`, `url`, `tel`, etc.) so mobile devices show the correct keyboard.
- Never disable paste on any field.

### 7.3 Cards

- Consistent padding (`--space-5`), corner radius (`--radius-md`), and border (`1px solid var(--color-border)`).
- Hover state on interactive cards: subtle elevation (`--shadow-md`) and/or a 1px accent border — not a full color change.

### 7.4 Project Attribution

Each tool is an independent application. It should not feel like a sub-page of the directory — it should feel like its own product. That said, every tool must include a small, unobtrusive attribution that connects it back to the project and its author.

- Every tool includes a small, consistent footer or corner attribution with: the tool's name, the Free Open Tools mark, and a link to `benjaminbarlow.com`.
- No "back to all tools" links, breadcrumbs, or directory nav. Each tool stands on its own.
- No nested menus, mega-menus, or hover-triggered dropdowns. Keep navigation flat.
- The attribution must be visible but not intrusive — small text, muted color, unobtrusive placement (bottom corner or below the fold).

### 7.5 Feedback & States

- Every action that takes >300ms shows a loading indicator (inline spinner or skeleton, using `--color-accent`).
- Every destructive action (delete, clear, reset) requires a confirmation step — a modal or inline "are you sure" — never a silent irreversible action.
- Empty states, errors, and success states always pair an icon with clear, plain-language text (no jargon, no stack traces shown to end users).
- Toasts/snackbars appear in a consistent position (bottom-center or bottom-right) across all tools, auto-dismiss after 4–6 seconds, but remain dismissible and pausable on hover/focus.

---

## 8. Light & Dark Mode

### 8.1 Requirements

- **Every application must support both light and dark mode.** No exceptions.
- Respect the user's OS preference by default via `prefers-color-scheme`, then allow manual override via a theme toggle that persists (localStorage) per tool, or globally if shared across subdomains.
- Theme switching must happen **without a page flash** — inline a theme-detection script in `<head>` before render, and set `data-theme` on `<html>` before first paint.

```html
<script>
  (function () {
    const saved = localStorage.getItem('theme');
    const theme = saved || (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
    document.documentElement.setAttribute('data-theme', theme);
  })();
</script>
```

### 8.2 Contrast

- All text/background combinations must meet **WCAG 2.1 AA**: 4.5:1 for normal text, 3:1 for large text (≥24px or 19px bold).
- Interactive/UI elements (borders, icons conveying state) must meet **3:1** minimum contrast against adjacent colors.
- Never disable or override the OS/browser focus outline without replacing it with an equally visible custom one (see §8.4).
- Test every theme combination with an automated contrast checker before shipping.

### 8.3 Never Rely on Color Alone

Any state or meaning conveyed by color (errors, success, links, active tabs) must have a second, non-color signal: an icon, underline, weight change, or text label. This protects colorblind users (~8% of men) and anyone in poor viewing conditions.

### 8.4 Focus States

- All interactive elements must have a clearly visible `:focus-visible` style — a 2px accent-colored outline with at least 2px offset from the element:
  ```css
  :focus-visible {
    outline: 2px solid var(--color-accent);
    outline-offset: 2px;
  }
  ```
- Never use `outline: none` without a compliant replacement.

---

## 9. Accessibility (WCAG 2.1 AA Minimum)

Accessibility is mandatory for every tool shipped in this ecosystem — not an enhancement.

### 9.1 Structure & Semantics

- Use real, semantic HTML elements (`<button>`, `<nav>`, `<main>`, `<label>`, `<table>`) before reaching for `<div>` + ARIA. ARIA is a supplement, not a substitute.
- One `<h1>` per page. Heading levels must be sequential (no skipping from `<h1>` to `<h3>`).
- All images/icons that convey meaning have descriptive `alt` text; purely decorative images use `alt=""`.
- Landmarks (`<header>`, `<main>`, `<footer>`, `<nav>`) are used so screen reader users can navigate by region.

### 9.2 Keyboard Access

- **Every interactive element must be reachable and operable via keyboard alone** — tab order follows visual/logical order.
- No keyboard traps. Modals trap focus intentionally while open and return focus to the triggering element on close.
- All custom components (dropdowns, tabs, sliders) follow WAI-ARIA Authoring Practices for expected keyboard behavior (Enter/Space to activate, Escape to close, arrow keys within composite widgets).
- Provide a "skip to main content" link as the first focusable element on every page.

### 9.3 Screen Readers

- Dynamic content changes (results, validation messages, loading states) are announced via `aria-live="polite"` (or `assertive` for critical errors) regions.
- Form controls are programmatically associated with their labels (`<label for>` or `aria-labelledby`).
- Icon-only buttons always include an accessible name via `aria-label` or visually-hidden text.

### 9.4 Motion & Sensory Considerations

- Respect `prefers-reduced-motion`: disable or drastically simplify non-essential animation when set.
  ```css
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
  }
  ```
- No auto-playing audio/video. No content that flashes more than 3 times per second (seizure risk).
- Never convey information through animation alone.

### 9.5 Touch & Pointer Devices

- Minimum interactive target size: **44×44px** (WCAG 2.5.5, Apple/Google guidelines), with at least 8px spacing between adjacent targets.
- Support both mouse and touch equally — no hover-only functionality. Anything triggered by `:hover` must have a touch/click/focus equivalent.
- Design for both portrait and landscape orientations; do not lock orientation.
- Test on real trackpads, touchscreens, and with keyboard-only navigation, not just mouse and desktop.

### 9.6 Responsive & Device Range

- Mobile-first responsive design; layouts must work from 320px width up through large desktop/ultra-wide displays.
- Text and controls must remain usable when the browser is zoomed to 200% (WCAG 1.4.4) without loss of content or function, and reflow properly up to 400% zoom without horizontal scrolling (WCAG 1.4.10).
- Support user font-size overrides — avoid fixed `px` heights on text containers that would clip enlarged text; prefer `rem`/`em` for typography-related sizing.

---

## 10. Performance as a UX Requirement

Because simplicity includes speed:

- No tool should ship a JS framework/bundle heavier than necessary for its function. Prefer vanilla JS/HTML/CSS or lightweight libraries.
- No unnecessary third-party scripts, ads, or trackers. If analytics are used, they must be privacy-respecting and disclosed.
- Target: first meaningful paint under 1 second on a median mobile connection.
- All tools must function fully offline-first where feasible (static, client-side processing) — this is both a performance and a privacy benefit central to the "serverless tools" mission.

---

## 11. Voice & Content

- Plain language always. No jargon, no marketing fluff, no exclamation points unless truly warranted.
- UI copy is short, direct, and action-oriented ("Copy result", not "Click here to copy your result to the clipboard!").
- Error messages explain what happened and how to fix it — never just "Something went wrong."
- No dark-pattern copy: no guilt-tripping cancel buttons, no countdown timers, no fake scarcity.

---

## 12. Checklist Before Shipping a New Tool

- [ ] Uses only the shared color tokens (black/white/gray + one blue accent)
- [ ] No emojis anywhere — flat SVG icons only
- [ ] Works correctly in both light and dark mode with no flash on load
- [ ] All text meets WCAG AA contrast in both themes
- [ ] All interactive elements are ≥44×44px with visible focus states
- [ ] Fully operable by keyboard alone, no traps
- [ ] Screen reader tested (VoiceOver/NVDA) for primary flow
- [ ] Respects `prefers-reduced-motion` and `prefers-color-scheme`
- [ ] No layout breakage at 320px width or 200% browser zoom
- [ ] One clear primary action; no competing CTAs
- [ ] No unnecessary dependencies, trackers, or dark patterns
- [ ] Includes a small, unobtrusive project attribution (tool name, Free Open Tools mark, link to benjaminbarlow.com)

---

*This is a living document. Improvements should raise the bar on simplicity and accessibility — never lower it for the sake of novelty.*
