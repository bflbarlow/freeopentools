# Free Open Tools — Ecosystem Style Guide

**Version 2.0**

This document is the single source of truth for the look, feel, and behavior of every application in the Free Open Tools suite. Every tool — regardless of what it does — must feel like it belongs to the same family. A user should be able to jump from one tool to another and instantly know how to use it.

These are not suggestions. They are rules. Deviation requires a documented reason.

---

## 1. Core Principles

1. **Simplicity first.** If a feature, control, or piece of copy isn't essential, remove it. Every screen should have one obvious primary action.
2. **Zero learning curve.** A first-time user should understand the interface in under 5 seconds, with no onboarding, tutorial, or tooltip required.
3. **Speed is a feature.** No unnecessary animations, loaders, or blocking states. Tools should feel instant.
4. **Consistency over creativity.** Individual tools should not invent their own design language. Novelty is spent on *functionality*, not chrome.
5. **App page is pure functionality.** The main application page contains only the tool's functionality — no footers, no attribution, no project links, no breadcrumbs, no "back to all tools" nav. Everything unrelated to the tool's purpose belongs on non-app pages (about, help, settings). The tool IS the page.
6. **Accessible by default, not by request.** Accessibility is not a checklist added at the end — it is a base requirement of every component.
7. **No dark patterns.** No fake urgency, no forced sign-ups, no hidden costs, no manipulative UI. Ever.
8. **No emojis.** Emojis render differently across operating systems, browsers, and device models — they break consistency, accessibility, and internationalization. Use flat, vector-based iconography (SVG) instead. Every tool must ship its own icons or use a shared icon set — never rely on the user's device to render the right glyph.

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
- Never rely on color alone to convey meaning (see Accessibility, §11.3).
- Pure black (`#000000`) and pure white (`#FFFFFF`) should be used sparingly as extremes — prefer the near-black/near-white values above, which reduce harsh contrast and eye strain.
- All color combinations used for text must meet WCAG AA contrast at minimum (see §10.2).

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
- **Content vs. workspace.** The term "content" in this section refers to **text, about pages, documentation, settings, and informational copy** — not the tool's functional workspace. Applications that require full viewport width for their primary function (diagram editors, canvases, image tools, code editors) should use `width: 100%` on their workspace area and are exempt from the max-width constraint. Text content within those applications (help text, descriptions, modals, settings forms) must still follow the rules below.
- **Text/about content** must be centered in a constrained container: `max-width: 720px` for primarily text-driven pages, `max-width: 960px–1100px` for pages with side-by-side panels (e.g., editors with preview panes, converters with split layouts). Do not exceed 1100px for any text/content container.
- **Tool workspace** (canvas, editor surface, output area) should take the full available viewport width/height and is not bound by the max-width rules above. The toolbar (see §7) and properties panel (see §8) handle the UI chrome; the workspace fills what remains.
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

## 7. App Toolbar

The App Toolbar is the primary navigation and control surface for every Free Open Tools application. It is the most recognisable structural element across the suite — users should feel at home the moment they see it, regardless of which tool they open. This section documents every implementation detail so all downstream tools can reproduce it exactly.

### 7.0 Component Index

The App Toolbar is composed of the following named sub-components, listed left to right in standard order:

| # | Component | ID / Class | Purpose |
|---|---|---|---|
| 1 | App Logo | `.app-logo` | Tool identification, links to about page |
| 2 | Theme Toggle | `#theme-toggle` | Light/dark mode switch |
| 3 | File Ops Group | `#btn-new`, `#btn-save`, `#btn-load` | New, Save, Load project files |
| 4 | Tool Mode Buttons | `[data-tool]` | Mode selection (Select, Line, Text, etc.) |
| 5 | Palette Buttons | `.shape-btn` | Shape/object placement buttons |
| 6 | Style Controls | `#fillColor`, `#strokeColor`, etc. | Color pickers, width/opacity inputs |
| 7 | History Actions | `#btn-undo`, `#btn-redo` | Undo/Redo navigation |
| 8 | Item Actions | `#btn-duplicate`, `#btn-delete` | Duplicate/Delete selected items |
| 9 | View Toggles | `#btn-grid`, `#btn-snap` | Grid/Snap overlay toggles |
| 10 | Zoom Controls | `#zoom-display`, `#btn-zoom-in`, `#btn-zoom-out` | Zoom level display and controls |
| 11 | Primary Action | `.tool-btn-primary` | The one dominant action per screen (Export, Save, etc.) |

Each group is separated by a `.toolbar-divider` (see §7.9). Not every tool needs every component — add only what your tool requires, but **preserve the left-to-right order** of any groups you do include.

### 7.1 Prerequisite CSS Variables

Before the toolbar can render, the following CSS custom properties must be defined in both light and dark themes. These go alongside the standard ecosystem palette from §2.

```css
:root {
    /* Full ecosystem reset — every tool must include this */
    box-sizing: border-box;
    --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
                 "Helvetica Neue", Arial, sans-serif;
    --font-mono: "SF Mono", "SFMono-Regular", Consolas, "Liberation Mono",
                 Menlo, monospace;
}
*, *::before, *::after { box-sizing: inherit; }

/* HTML/body must fill the viewport for fixed-position chrome to work */
html, body {
    width: 100%; height: 100%;
    margin: 0; padding: 0;
    overflow: hidden;
    font-family: var(--font-sans);
    background: var(--color-bg);
    color: var(--color-text);
}

:root,
[data-theme="dark"] {
    --color-icon-default:        #A3A3AB;
    --color-icon-hover:          #F2F2F3;
    --color-btn-active-bg:       #2C2C32;
    --color-btn-hover-bg:        #2C2C32;
    --color-btn-hover-border:    #2C2C32;
    --color-toolbar-divider:     #2C2C32;
    --color-input-bg:            #1C1C20;
    --color-border-input:        #45475A;
    --color-canvas-bg:           #1C1C20;
    --color-oob-bg:              #17171A;
    --color-scrollbar-track:     #17171A;
    --color-scrollbar-thumb:     #2C2C32;
    --color-scrollbar-thumb-hover: #45475A;
}

[data-theme="light"] {
    --color-icon-default:        #9A9AA1;
    --color-icon-hover:          #111113;
    --color-btn-active-bg:       #E2E2E5;
    --color-btn-hover-bg:        #F5F5F7;
    --color-btn-hover-border:    #E2E2E5;
    --color-toolbar-divider:     #E2E2E5;
    --color-input-bg:            #FFFFFF;
    --color-border-input:        #E2E2E5;
    --color-canvas-bg:           #FFFFFF;
    --color-oob-bg:              #F5F5F7;
    --color-scrollbar-track:     #F5F5F7;
    --color-scrollbar-thumb:     #E2E2E5;
    --color-scrollbar-thumb-hover: #9A9AA1;
}
```

### 7.2 Inline Theme-Detection Script (FOUC Prevention)

Every tool must include this inline script in `<head>` **before** the `<link rel="stylesheet">` tag. This prevents a flash of incorrectly-themed content (FOUC) on load.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>tool-name</title>
    <script>
      (function () {
        var saved = localStorage.getItem('toolname-theme');
        var theme = saved || (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
        document.documentElement.setAttribute('data-theme', theme);
      })();
    </script>
    <link rel="stylesheet" href="styles.css">
</head>
```

- The localStorage key should follow the pattern `toolname-theme` (e.g., `diagram-theme`, `markdown-theme`).
- Respects the OS `prefers-color-scheme` as the default when no saved preference exists.
- The script runs synchronously before the stylesheet loads, so `data-theme` is set on `<html>` by the time CSS is parsed.
- **Important**: This script only sets `data-theme`. The SVG visibility for the theme toggle button (sun/moon icons) is fixed by JS on DOM ready (see §7.18). The HTML markup in §7.3 renders both SVGs; the init JS hides/shows them to match the loaded theme. Without the init code, the sun icon will show briefly even in dark mode.

### 7.3 Full Toolbar HTML Structure

This is the exact HTML template for the toolbar wrapper. Every tool must replicate this structure, replacing tool-specific buttons as needed.

```html
<body>
    <!-- Visually-hidden H1 for screen readers (see §7.5) -->
    <h1 class="sr-only">Tool Name — Description</h1>

    <!-- Skip-to-content link (see §7.6) -->
    <a href="#main-workspace" class="skip-link">Skip to main content</a>

    <!-- Toolbar -->
    <div id="toolbar">
        <!-- App logo (links to about page) -->
        <a class="app-logo" href="about.html">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:middle;margin-right:4px">…</svg>
            tool-name
        </a>
        <div class="toolbar-divider"></div>

        <!-- Tool buttons (mode selection) -->
        <button class="tool-btn active" data-tool="select" title="Select (V)"><svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="…"/></svg></button>
        <button class="tool-btn" data-tool="other" title="Other (O)"><svg …></svg></button>
        <div class="toolbar-divider"></div>

        <!-- Action buttons -->
        <button class="tool-btn" id="btn-undo" title="Undo (Ctrl+Z)"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">…</svg></button>
        <button class="tool-btn" id="btn-redo" title="Redo (Ctrl+Y)"><svg …></svg></button>
        <div class="toolbar-divider"></div>

        <!-- View toggles -->
        <button class="tool-btn active" id="btn-grid" title="Toggle Grid (G)"><svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">…</svg></button>
        <div class="toolbar-divider"></div>

        <!-- Zoom controls (optional) -->
        <span class="zoom-display" id="zoom-display">100%</span>
        <button class="tool-btn" id="btn-zoom-in" title="Zoom In">+</button>
        <button class="tool-btn" id="btn-zoom-out" title="Zoom Out">−</button>
        <div class="toolbar-divider"></div>

        <!-- Theme toggle -->
        <button id="theme-toggle" title="Toggle theme">
            <svg class="theme-sun" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="5"/><path d="M12 1v2M12 21v2M4.22 4.22l1.42 1.42M18.36 18.36l1.42 1.42M1 12h2M21 12h2M4.22 19.78l1.42-1.42M18.36 5.64l1.42-1.42"/></svg>
            <svg class="theme-moon" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="display:none"><path d="M21 12.79A9 9 0 1111.21 3 7 7 0 0021 12.79z"/></svg>
        </button>
        <div class="toolbar-divider"></div>

        <!-- Primary action (export, save, etc.) -->
        <button class="tool-btn-primary" id="btn-export" title="Export"><svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="…"/></svg></button>
    </div>

    <!-- Main workspace -->
    <div id="main-workspace">
        …
    </div>
</body>
```

### 7.4 Global CSS Reset / Base Styles

Every tool must start with this reset to ensure consistent box-model, font rendering, and viewport filling. Without it, fixed-position chrome (toolbar, panel) will not behave identically across tools.

```css
* { margin: 0; padding: 0; box-sizing: border-box; }

html, body {
    width: 100%; height: 100%;
    overflow: hidden;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto,
                 'Helvetica Neue', Arial, sans-serif;
    background: var(--color-bg);
    color: var(--color-text);
}
```

- `box-sizing: border-box` on all elements ensures padding and border are inside declared widths. If omitted, the fixed-position toolbar and panel will calculate widths differently between tools.
- `html, body { overflow: hidden; }` prevents unwanted page scroll on apps that use fixed-position chrome.
- The font-family must be the same across all tools (system font stack from §5.1). If one tool uses a webfont or a different stack, the logo text and any toolbar text labels will render differently.

### 7.5 Visually-Hidden H1 (Screen Reader Landmark)

The app page must have exactly one `<h1>` for screen reader navigation. Since the toolbar is a visual-only navigation structure, the `<h1>` is hidden visually but present in the DOM.

```css
.sr-only {
    position: absolute;
    width: 1px; height: 1px;
    padding: 0; margin: -1px;
    overflow: hidden;
    clip: rect(0,0,0,0);
    white-space: nowrap;
    border: 0;
}
```

```html
<h1 class="sr-only">Diagram — Canvas Editor</h1>
```

### 7.6 Skip-to-Content Link

The skip link must be the **first focusable element** in `<body>`, placed immediately after the `<h1>`.

```html
<a href="#main-workspace" class="skip-link">Skip to main content</a>
```

```css
.skip-link {
    position: absolute;
    top: -100%;
    left: 0;
    z-index: 10000;
    padding: var(--space-2) var(--space-4);
    background: var(--color-accent);
    color: #fff;
    text-decoration: none;
    font-size: 16px;
    font-weight: 600;
    border-radius: 0 0 var(--radius-sm) 0;
}
.skip-link:focus {
    top: 0;
    outline: 2px solid var(--color-accent);
    outline-offset: 2px;
}
```

- The link is positioned off-screen (`top: -100%`) until focused.
- On focus, it slides in at `top: 0` (left edge of viewport).
- The `href` must match the `id` of the main workspace container.

### 7.7 Layout & Dimensions

- **Position**: Fixed to the top of the viewport (`position: fixed; top: 0; left: 0; right: 0`).
- **Height**: Exactly **60px**. Do not vary this height across tools.
- **Background**: `--color-surface` with a single `1px solid var(--color-border)` bottom border.
- **Layout**: Horizontal flexbox (`display: flex; align-items: center`), with items centered vertically.
- **Padding**: `0 var(--space-3)` (0 12px) horizontally.
- **Gap**: `var(--space-1)` (4px) between items.
- **Z-index**: `1000` — always on top.
- **Overflow**: `overflow-x: auto; overflow-y: hidden` with `-webkit-overflow-scrolling: touch` so the toolbar scrolls horizontally on narrow viewports.
- **Touch**: `touch-action: manipulation` to prevent double-tap zoom.

```css
#toolbar {
    position: fixed; top: 0; left: 0; right: 0;
    height: 60px;
    background: var(--color-surface);
    border-bottom: 1px solid var(--color-border);
    display: flex; align-items: center;
    padding: 0 var(--space-3);
    z-index: 1000;
    gap: var(--space-1);
    touch-action: manipulation;
    overflow-x: auto;
    overflow-y: hidden;
    -webkit-overflow-scrolling: touch;
}
```

### 7.8 App Logo

- Positioned leftmost in the toolbar.
- **Font**: `16px`, `font-weight: 700`.
- **Color**: `var(--color-accent)` with `var(--color-accent-hover)` on hover.
- **Letter-spacing**: `-0.5px` for a tighter, modern look.
- **White-space**: `nowrap` to prevent wrapping.
- **Decoration**: No underline (`text-decoration: none`).
- **Cursor**: Pointer.
- **Icon**: An inline SVG (16×16 viewBox) sits before the text, styled with `vertical-align:middle` and `margin-right:4px`.
- **Link**: Links to the application's about page. Never links to the ecosystem directory — each tool stands on its own.

```html
<a class="app-logo" href="about.html">
    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="vertical-align:middle;margin-right:4px">…</svg>
    tool-name
</a>
```

```css
.app-logo {
    font-size: 16px; font-weight: 700;
    color: var(--color-accent);
    margin-right: var(--space-2);
    user-select: none;
    letter-spacing: -0.5px;
    white-space: nowrap;
    text-decoration: none;
    cursor: pointer;
}
.app-logo:hover {
    color: var(--color-accent-hover);
}
```

### 7.9 Toolbar Dividers

Logical groups of controls are separated by vertical dividers:

```css
.toolbar-divider {
    width: 1px; height: 32px;
    background: var(--color-toolbar-divider);
    margin: 0 var(--space-1);
    flex-shrink: 0;
}
```

- `height: 32px` (roughly half the toolbar height) leaves breathing room above and below.
- `flex-shrink: 0` ensures dividers never collapse.

### 7.10 Toolbar Buttons (`.tool-btn`)

Standard icon buttons for tools, actions, and toggles. This is the most common button type in the toolbar.

```css
.tool-btn {
    width: 44px; height: 44px;
    min-width: 44px; min-height: 44px;
    border: none;
    background: transparent;
    color: var(--color-icon-default);
    border-radius: var(--radius-sm);
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    transition: all 0.15s;
    flex-shrink: 0;
}

.tool-btn:hover {
    background: var(--color-btn-hover-bg);
    color: var(--color-icon-hover);
}

.tool-btn.active {
    background: var(--color-btn-active-bg);
    color: var(--color-accent);
    box-shadow: inset 0 0 0 1.5px var(--color-accent);
    position: relative;
}

.tool-btn.active::after {
    content: '';
    position: absolute;
    bottom: 2px;
    left: 50%;
    transform: translateX(-50%);
    width: var(--space-1);
    height: var(--space-1);
    border-radius: 50%;
    background: var(--color-accent);
}

.tool-btn:active {
    transform: scale(0.95);
}
```

Rules:
- **Minimum 44×44px** touch target (WCAG 2.5.5). Both `width`/`height` and `min-width`/`min-height` must be set for safe rendering.
- **Icon color**: `--color-icon-default` (muted) by default, switches to `--color-icon-hover` (full text color) on hover.
- **Active state**: Accent background tint (`--color-btn-active-bg`), accent inset border via `box-shadow`, and a small 4px accent dot below the icon as a secondary non-color signal.
- **Icons**: SVG with 24×24 viewBox, `stroke="currentColor"`, `stroke-width="2"`. Icon sizes within the SVG should be 22×22 for tool mode buttons (room for padding) or 18×18 for utility buttons (undo, redo, actions).
- Each button must have a `title` attribute describing both the action and its keyboard shortcut (e.g., `title="Select (V)"`).
- `flex-shrink: 0` prevents buttons from compressing on narrow screens.

### 7.11 SVG Icon Conventions

All SVG icons in toolbar buttons follow these exact rules (sourced from the diagram editor implementation):

```html
<!-- Tool mode icon: 22×22 visible path in 24×24 viewBox -->
<svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
    <path d="M3 3l7.07 16.97 2.51-7.39 7.39-2.51L3 3z"/>
</svg>

<!-- Utility icon: 18×18 visible path in 24×24 viewBox -->
<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
    <polyline points="1 4 1 10 7 10"/>
    <path d="M3.51 15a9 9 0 1 0 2.13-9.36L1 10"/>
</svg>
```

- `viewBox="0 0 24 24"` — always. This ensures consistent scaling across all icons in the ecosystem.
- `fill="none"` — icons are stroke-based, not filled.
- `stroke="currentColor"` — icons inherit the button's `color` property, which changes based on states (default → hover → active).
- `stroke-width="2"` — consistent line weight across all icons.
- Tool mode icons are sized `width="22" height="22"` (leaves 2px padding within the 44×44 button).
- Utility icons are sized `width="18" height="18"` (more compact).
- The theme toggle SVGs are an exception: `width="18" height="18"` with `viewBox="0 0 24 24"`.
- **Never use emoji characters, Unicode glyphs, or external icon fonts.**

### 7.12 Primary Action Button (`.tool-btn-primary`)

Each tool must have one visually dominant primary action. This button uses the ecosystem's solid accent style and sits near the right end of the toolbar.

```css
.tool-btn-primary {
    width: 44px; height: 44px;
    min-width: 44px; min-height: 44px;
    border: none;
    background: var(--color-accent);
    color: #fff;
    border-radius: var(--radius-sm);
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    transition: all 0.15s;
    flex-shrink: 0;
}
.tool-btn-primary:hover {
    background: var(--color-accent-hover);
    color: #fff;
}
.tool-btn-primary:active {
    transform: scale(0.95);
}
```

- Solid accent background (`--color-accent`), white text/icon.
- Reserved for the one primary action per screen (e.g., "Export", "Convert", "Generate").
- Do not create multiple primary buttons — only one per view.
- If the primary action has sub-options (e.g., different export formats), wrap it in a `.export-wrap` container (see §7.19).

### 7.13 Shape / Palette Buttons (`.shape-btn`)

Used when the toolbar contains a palette of items the user can click to place (e.g., shapes in a diagram editor, format buttons in a markdown editor):

```css
.shape-btn {
    width: 44px; height: 44px;
    min-width: 44px; min-height: 44px;
    border: 1px solid transparent;
    background: transparent;
    color: var(--color-icon-default);
    border-radius: var(--radius-sm);
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    transition: all 0.15s;
    flex-shrink: 0;
}

.shape-btn:hover {
    background: var(--color-btn-hover-bg);
    color: var(--color-icon-hover);
    border-color: var(--color-btn-hover-border);
}

.shape-btn.active {
    background: var(--color-btn-active-bg);
    color: var(--color-accent);
    border-color: var(--color-accent);
    position: relative;
}

.shape-btn.active::after {
    content: '';
    position: absolute;
    bottom: 2px;
    left: 50%;
    transform: translateX(-50%);
    width: var(--space-1);
    height: var(--space-1);
    border-radius: 50%;
    background: var(--color-accent);
}

.shape-btn svg {
    width: 22px; height: 18px;
}
```

- Same 44×44px minimum as `.tool-btn`.
- Preview/preview SVG inside the button uses natural viewBox for the preview (no fixed 24×24 constraint — let the preview dictate its own aspect ratio).
- Has a `border: 1px solid transparent` that becomes visible on hover/active — this distinguishes palette buttons from tool mode buttons.
- Active state includes the same accent dot indicator as `.tool-btn.active`.

### 7.14 Inline Toolbar Controls

Some tools need quick-access controls directly in the toolbar. These must match the 44px height of buttons.

**Color input** (`input[type="color"]`):
```css
#toolbar input[type="color"] {
    width: 44px; height: 44px;
    min-width: 44px; min-height: 44px;
    border: 2px solid var(--color-border-input);
    border-radius: var(--radius-sm);
    cursor: pointer;
    padding: 2px;
    background: none;
    flex-shrink: 0;
}
#toolbar input[type="color"]::-webkit-color-swatch-wrapper { padding: 0; }
#toolbar input[type="color"]::-webkit-color-swatch { border: none; border-radius: 2px; }
```

**Number input** (`input[type="number"]`):
```css
#toolbar > input[type="number"] {
    width: 48px; height: 44px;
    min-height: 44px;
    background: var(--color-input-bg);
    border: 1px solid var(--color-border-input);
    border-radius: var(--radius-sm);
    color: var(--color-text);
    font-size: 16px;
    text-align: center;
    padding: 2px;
    flex-shrink: 0;
}
#toolbar > input[type="number"]:focus {
    border-color: var(--color-accent);
    outline: none;
}
```

> The `> child` selector (`#toolbar > input[type="number"]`) ensures this only applies to direct children of the toolbar, not number inputs inside dropdowns or panels.

**Range slider** (`input[type="range"]`):
```css
#toolbar input[type="range"] {
    width: 64px; height: 44px;
    -webkit-appearance: none;
    appearance: none;
    background: transparent;
    border-radius: 2px;
    outline: none;
    flex-shrink: 0;
    cursor: pointer;
    padding: 0 var(--space-2);
}
#toolbar input[type="range"]::-webkit-slider-runnable-track {
    width: 100%; height: 6px;
    background: var(--color-border-input);
    border-radius: var(--radius-sm);
}
#toolbar input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 20px; height: 20px;
    background: var(--color-accent);
    border-radius: 50%;
    cursor: pointer;
    margin-top: -7px;
}
#toolbar input[type="range"]::-moz-range-track {
    height: 6px;
    background: var(--color-border-input);
    border-radius: var(--radius-sm);
}
#toolbar input[type="range"]::-moz-range-thumb {
    width: 20px; height: 20px;
    background: var(--color-accent);
    border-radius: 50%;
    border: none;
    cursor: pointer;
}
```

Rules:
- All toolbar inputs must be at least **44px tall** (touch target minimum).
- Input font size must be **16px minimum** to prevent iOS Safari auto-zoom on focus.
- Range sliders need both `-webkit-appearance` and `-moz-appearance` overrides for cross-browser support.
- The slider thumb must be at least 20×20px for touch usability.

### 7.15 Zoom Display

When a tool uses zoom/scale controls, the current zoom level is shown as a muted text label between zoom buttons:

```html
<span class="zoom-display" id="zoom-display">100%</span>
```

```css
.zoom-display {
    font-size: 12px;
    color: var(--color-text-muted);
    padding: 0 var(--space-1);
    user-select: none;
    white-space: nowrap;
}
```

### 7.16 Theme Toggle — HTML Markup

```html
<button id="theme-toggle" title="Toggle theme">
    <!-- Sun icon: visible in light mode, hidden in dark mode -->
    <svg class="theme-sun" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <circle cx="12" cy="12" r="5"/>
        <path d="M12 1v2M12 21v2M4.22 4.22l1.42 1.42M18.36 18.36l1.42 1.42M1 12h2M21 12h2M4.22 19.78l1.42-1.42M18.36 5.64l1.42-1.42"/>
    </svg>
    <!-- Moon icon: hidden in light mode, visible in dark mode -->
    <svg class="theme-moon" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="display:none">
        <path d="M21 12.79A9 9 0 1111.21 3 7 7 0 0021 12.79z"/>
    </svg>
</button>
```

- **Both SVGs are always in the DOM** — the visible one is toggled via `display` (never swap innerHTML).
- The sun icon (`.theme-sun`) is shown by default (no `style="display:none"`).
- The moon icon (`.theme-moon`) starts hidden (`style="display:none"`).
- Each SVG is 18×18 with the standard 24×24 viewBox, `fill="none"`, `stroke="currentColor"`, `stroke-width="2"`.

### 7.17 Theme Toggle — CSS

```css
#theme-toggle {
    width: 44px; height: 44px;
    min-width: 44px; min-height: 44px;
    border: none;
    background: transparent;
    color: var(--color-icon-default);
    border-radius: var(--radius-sm);
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    transition: all 0.15s;
    flex-shrink: 0;
    font-size: 18px;
}
#theme-toggle:hover {
    background: var(--color-btn-hover-bg);
    color: var(--color-icon-hover);
}
```

### 7.18 Theme Toggle — JavaScript

The toggle handler must:
1. Swap the `data-theme` attribute on `<html>` between `"light"` and `"dark"`
2. Toggle SVG visibility (sun shown in light mode, moon in dark mode)
3. Persist the preference to `localStorage`
4. Update any rendered elements that depend on theme CSS variables

```js
// Theme toggle click handler
document.getElementById('theme-toggle').addEventListener('click', function() {
    var html = document.documentElement;
    var isDark = html.dataset.theme !== 'light';
    html.dataset.theme = isDark ? 'light' : 'dark';
    // Toggle SVG icons
    var sun = this.querySelector('.theme-sun');
    var moon = this.querySelector('.theme-moon');
    if (sun) sun.style.display = isDark ? 'block' : 'none';
    if (moon) moon.style.display = isDark ? 'none' : 'block';
    localStorage.setItem('toolname-theme', isDark ? 'light' : 'dark');
    // Re-read theme-dependent CSS variables
    readTheme();
    // Re-render any themed elements
    // (tool-specific: grid, connections, canvas, etc.)
});
```

On page load, the SVG visibility must match the restored theme (the inline `<head>` script already set `data-theme`):

```js
// On init (after DOM ready): set theme toggle SVGs to match loaded theme
var savedTheme = localStorage.getItem('toolname-theme') || 'dark';
document.documentElement.dataset.theme = savedTheme;
// Set theme toggle SVG visibility
(function() {
    var toggle = document.getElementById('theme-toggle');
    if (!toggle) return;
    var sun = toggle.querySelector('.theme-sun');
    var moon = toggle.querySelector('.theme-moon');
    if (sun) sun.style.display = savedTheme === 'light' ? 'none' : 'block';
    if (moon) moon.style.display = savedTheme === 'light' ? 'block' : 'none';
})();
```

**Cross-tab theme sync** — When the user changes theme in one tab, all other open tabs should update instantly via the `storage` event:

```js
window.addEventListener('storage', function(e) {
    if (e.key === 'toolname-theme' && e.newValue) {
        document.documentElement.dataset.theme = e.newValue;
        var toggle = document.getElementById('theme-toggle');
        if (toggle) {
            var sun = toggle.querySelector('.theme-sun');
            var moon = toggle.querySelector('.theme-moon');
            if (sun) sun.style.display = e.newValue === 'light' ? 'none' : 'block';
            if (moon) moon.style.display = e.newValue === 'light' ? 'block' : 'none';
        }
        // Re-read CSS vars and re-render themed content
        readTheme();
        // tool-specific re-render calls here
    }
});
```

### 7.19 Export / Dropdown Pattern

When a primary action needs sub-options (e.g., Export with multiple formats), use a wrapping container with an anchored dropdown:

```html
<div class="export-wrap">
    <button class="tool-btn-primary" id="btn-export" title="Export">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">…</svg>
    </button>
    <div class="export-drop" id="export-drop">
        <div class="export-item" data-action="action-1">Option 1</div>
        <div class="export-item" data-action="action-2">Option 2</div>
    </div>
</div>
```

```css
.export-wrap {
    position: relative;
}

.export-drop {
    position: absolute;
    top: 100%;
    right: 0;
    margin-top: var(--space-1);
    background: var(--color-surface);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-md);
    padding: var(--space-1);
    display: none;
    flex-direction: column;
    z-index: 200;
    min-width: 160px;
    box-shadow: var(--shadow-md);
}

.export-drop.visible {
    display: flex;
}

.export-item {
    padding: var(--space-3) var(--space-4);
    font-size: 16px;
    color: var(--color-text);
    cursor: pointer;
    border-radius: var(--radius-sm);
    user-select: none;
    min-height: 44px;
    display: flex;
    align-items: center;
}

.export-item:hover {
    background: var(--color-input-bg);
    color: var(--color-accent);
}
```

- The dropdown is positioned below the button, right-aligned.
- Items are 44px minimum height for touch targets.
- Hover highlights the item with input background and accent text.
- Toggle via `.visible` class on the dropdown element.
- Close on click-outside or Escape key.

### 7.20 Global Focus States

All interactive elements must have a visible focus ring:

```css
:focus-visible {
    outline: 2px solid var(--color-accent);
    outline-offset: 2px;
}
```

This must be defined at the `:root` level so it applies globally. Never use `outline: none` without a compliant replacement.

### 7.21 Scrollbar Styling

For the toolbar (and any scrollable panels), style the scrollbar to match the ecosystem's muted aesthetic:

```css
::-webkit-scrollbar {
    width: var(--space-2);
    height: var(--space-2);
}
::-webkit-scrollbar-track {
    background: var(--color-scrollbar-track);
}
::-webkit-scrollbar-thumb {
    background: var(--color-scrollbar-thumb);
    border-radius: var(--radius-sm);
}
::-webkit-scrollbar-thumb:hover {
    background: var(--color-scrollbar-thumb-hover);
}
```

### 7.22 Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.01ms !important;
    }
}
```

### 7.23 Toolbar Button Grouping Reference

The standard toolbar layout (left to right) follows this logical order. Every tool adapts this pattern to its own needs, but the order of groups should remain consistent:

1. **App logo** — links to about page, identifies the tool (§7.8)
2. **Tool mode buttons** — Select, Line, Text, etc. (§7.10)
3. **Palette items** — shapes, format toggles, draggable elements (§7.13)
4. **Style controls** — color, width, opacity, font controls (§7.14)
5. **Undo / Redo** — history navigation (§7.10)
6. **Item actions** — Duplicate, Delete (§7.10)
7. **View toggles** — Grid, Snap, overlays (§7.10)
8. **Zoom controls** — zoom level display, zoom in, zoom out (§7.15)
9. **Theme toggle** — light/dark switch (§7.16–7.18)
10. **Primary action** — Export, Save, Generate (last right-aligned group, §7.12)

### 7.24 Responsive Behavior

The toolbar scrolls horizontally on narrow screens (already handled by `overflow-x: auto`). For additional responsive adjustments:

```css
@media (max-width: 480px) {
    #toolbar {
        gap: 2px;
        padding: 0 var(--space-1);
    }
    /* Hide non-critical buttons on very narrow screens */
    .tool-btn.optional {
        display: none;
    }
}
```

Each tool may choose which buttons to mark `.optional` based on its own feature set. The core requirement is that the toolbar remains usable and scrollable at 320px viewport width.

---

## 8. Properties Pane

The Properties Pane is the secondary inspection and editing surface for every Free Open Tools application. When present, it must follow these conventions for consistency across the suite. Not every tool requires a properties pane — include it only when your tool needs object inspection/editing.

### 8.0 Component Index

The Properties Pane is composed of the following named sub-components:

| # | Component | ID / Class | Purpose |
|---|---|---|---|
| 1 | Panel Container | `#properties-panel` | Outer wrapper, positioned fixed right |
| 2 | Resize Handle | `#panel-resize-handle` | Drag to resize panel width |
| 3 | Panel Header | `#panel-header` | Title + tab bar + collapse button |
| 4 | Panel Title | `#panel-title` | Dynamic context label (e.g., "Shape", "Connection", "Project") |
| 5 | Tab Bar | `.panel-tabs` | Properties / Layers tab switching |
| 6 | Tab Buttons | `.panel-tab[data-tab]` | Individual tab triggers |
| 7 | Collapse Button | `#btn-collapse-panel` | Collapse/expand the panel |
| 8 | Scroll Container | `.panel-scroll` | Scrollable property content |
| 9 | Panel Sections | `.panel-section` | Grouped property groups |
| 10 | Action Log | `.panel-log` | Collapsible audit trail of actions |
| 11 | Log Toggle | `#log-toggle` | Expand/collapse the action log |
| 12 | Log Body | `#log-body` | Scrollable log entries (aria-live="polite") |

### 8.1 Layout & Dimensions

- **Position**: Fixed to the right side of the viewport (`position: fixed; top: 60px; right: 0; bottom: 0`). Positions below the toolbar (60px offset).
- **Width**: **220px** — narrow enough to leave room for the main workspace on a 1024px screen.
- **Background**: `--color-surface` with `1px solid var(--color-border)` left border.
- **Layout**: Flex column — the scrollable content area fills the space, and a collapsible action log sits at the bottom.
- **Z-index**: `900` — below the toolbar (1000) but above the canvas.
- **Transition**: `width 0.2s` for smooth collapse/expand.

```css
#properties-panel {
    position: fixed; top: 60px; right: 0; bottom: 0;
    width: 220px;
    background: var(--color-surface);
    border-left: 1px solid var(--color-border);
    display: flex;
    flex-direction: column;
    z-index: 900;
    transition: width 0.2s;
}
#properties-panel.hidden { display: none; }
```

### 8.2 Collapsed State

The panel can collapse to a thin vertical strip for users who need maximum workspace:

```css
#properties-panel.collapsed {
    width: 36px;
    min-width: 36px;
}
#properties-panel.collapsed .panel-scroll,
#properties-panel.collapsed .panel-log,
#properties-panel.collapsed .panel-header span { display: none; }
#properties-panel.collapsed .panel-header {
    writing-mode: vertical-rl;
    padding: var(--space-2) var(--space-1);
    border-bottom: none;
    cursor: pointer;
    flex: 1;
    justify-content: center;
    gap: var(--space-1);
}
```

- Collapsed width: **36px** — just enough for a vertical label and expand button.
- The panel header text rotates vertically via `writing-mode: vertical-rl`.
- All content (scroll, log) is hidden; clicking the header expands the panel.

### 8.3 Panel Header

```css
.panel-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 14px; font-weight: 600;
    color: var(--color-accent);
    padding: var(--space-3);
    border-bottom: 1px solid var(--color-border);
    flex-shrink: 0;
}
.panel-header button {
    display: flex;
    align-items: center;
    justify-content: center;
    background: var(--color-canvas-bg);
    border: 1px solid var(--color-border);
    color: var(--color-accent);
    cursor: pointer;
    padding: var(--space-1) 7px;
    border-radius: var(--radius-sm);
    font-size: 16px;
    line-height: 1;
    font-weight: 700;
}
.panel-header button:hover {
    color: var(--color-accent-hover);
    border-color: var(--color-accent);
    background: var(--color-btn-hover-bg);
}
```

- The header shows the current context (e.g., "Shape", "Connection", "Project") in accent-colored bold text.
- A collapse button (chevron icon) is positioned on the right.

### 8.4 Panel Sections & Labels

Content is divided into labelled sections separated by borders:

```css
.panel-section {
    padding: var(--space-3);
    border-bottom: 1px solid var(--color-border);
}
.panel-section label {
    display: block;
    font-size: 12px;
    color: var(--color-text-muted);
    margin-bottom: var(--space-1);
    margin-top: var(--space-2);
}
.panel-section label:first-child { margin-top: 0; }
```

- Labels are small (12px) and muted to de-emphasise them relative to their values.
- Sections stack vertically; each section is separated by `--color-border`.

### 8.5 Panel Inputs

All inputs within the properties panel follow a consistent style:

```css
.panel-section input[type="number"],
.panel-section input[type="color"],
.panel-section textarea {
    width: 100%;
    padding: var(--space-1) var(--space-2);
    background: var(--color-input-bg);
    border: 1px solid var(--color-border-input);
    border-radius: var(--radius-sm);
    color: var(--color-text);
    font-size: 16px;
    outline: none;
    font-family: inherit;
    resize: vertical;
}
.panel-section input[type="number"]:focus,
.panel-section input[type="color"]:focus,
.panel-section textarea:focus {
    border-color: var(--color-accent);
}
.panel-section input[type="color"] {
    height: 44px;
    min-height: 44px;
    cursor: pointer;
}
.panel-section input[type="range"] {
    width: 100%;
    height: 4px;
    -webkit-appearance: none;
    appearance: none;
    background: var(--color-border-input);
    border-radius: 2px;
    outline: none;
    cursor: pointer;
    margin-top: var(--space-1);
}
.panel-section input[type="range"]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 14px; height: 14px;
    background: var(--color-accent);
    border-radius: 50%;
    cursor: pointer;
}
```

Rules:
- **Full width** — inputs fill their section horizontally.
- **16px font size minimum** — prevents iOS auto-zoom.
- **Focus state** — accent border replaces the default outline.
- Color inputs get 44px height for touch accessibility.
- Sliders use a small accent-colored thumb (14×14px).

### 8.6 Panel Section Visibility

Sections are shown or hidden based on the current selection context:

```css
.panel-shape.hidden,
.panel-conn.hidden,
.panel-custom.hidden,
.panel-project.hidden { display: none; }
```

Context-driven visibility:
| Selection | Visible Sections |
|---|---|
| Nothing selected | Project (name, canvas dimensions) |
| Single shape | Shape (geometry, text, style) |
| Single connection | Connection (name, color, width, arrows) |
| Custom shape | Shape (geometry, text) + Custom SVG (viewBox, SVG code) |
| Multiple or mixed | Project (fallback) |

### 8.7 Action Log (Panel Footer)

A collapsible action log lives at the bottom of the properties panel, providing real-time feedback:

```css
.panel-log {
    border-top: 1px solid var(--color-border);
    overflow: hidden;
    max-height: var(--space-5);
    transition: max-height 0.2s;
    flex-shrink: 0;
}
.panel-log.open { max-height: 200px; }
.log-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 2px var(--space-3);
    font-size: 12px;
    color: var(--color-text-muted);
    cursor: pointer;
    user-select: none;
    flex-shrink: 0;
}
.log-body {
    flex: 1;
    overflow-y: auto;
    padding: 2px var(--space-3) var(--space-1);
    font-family: var(--font-mono);
    font-size: 12px;
    color: var(--color-text-muted);
    line-height: 1.5;
}
```

- Collapsed by default (`max-height: 24px` showing only the header).
- Expanded to **200px max-height** via `.open` class.
- Renders in `--font-mono` for clear visual distinction from UI text.
- Uses `aria-live="polite"` on the log body so screen readers announce new entries.

### 8.8 Responsive Behavior

The properties panel is hidden on narrow screens to preserve workspace:

```css
@media (max-width: 768px) {
    #properties-panel {
        display: none !important;
    }
}
```

Applications that require properties on mobile should consider an overlay or bottom-sheet pattern as an enhancement.

---

## 9. Components

### 9.1 Buttons

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

### 9.2 Forms & Inputs

- Every input has a **visible, persistent label** — never rely on placeholder text as a label (it disappears on input and fails accessibility).
- Inputs have a minimum height of 44px on touch devices.
- Font size in inputs is **16px minimum** to prevent mobile browsers (iOS Safari) from auto-zooming on focus.
- Validation errors appear inline, next to the field, with text + icon (not color alone), and are announced to screen readers via `aria-describedby` and `aria-invalid`.
- Use native HTML input types (`email`, `number`, `url`, `tel`, etc.) so mobile devices show the correct keyboard.
- Never disable paste on any field.

### 9.3 Cards

- Consistent padding (`--space-5`), corner radius (`--radius-md`), and border (`1px solid var(--color-border)`).
- Hover state on interactive cards: subtle elevation (`--shadow-md`) and/or a 1px accent border — not a full color change.

### 9.4 Project Attribution

Each tool is an independent application. It should not feel like a sub-page of the directory — it should feel like its own product.

- **The main app page is attribution-free.** No footers, no project marks, no author links, no breadcrumbs, no "back to all tools" navigation. The app page is dedicated exclusively to the tool's functionality. See Core Principle §1.5.
- **Non-app pages** (about, help, settings, landing) should connect the tool back to the Free Open Tools project and its author. Include: the tool's name, the Free Open Tools mark ("open" in accent blue), and a link to `benjaminbarlow.com`.
- **Placement**: In footer or bottom area of non-app pages — small text, muted color, unobtrusive.
- No nested menus, mega-menus, or hover-triggered dropdowns anywhere in the suite. Keep navigation flat.
- The logo in the toolbar (§7.8) links to the tool's about page. That about page is the natural home for project attribution and ecosystem links — not the app page itself.

### 9.5 Feedback & States

- Every action that takes >300ms shows a loading indicator (inline spinner or skeleton, using `--color-accent`).
- Every destructive action (delete, clear, reset) requires a confirmation step — a modal or inline "are you sure" — never a silent irreversible action.
- Empty states, errors, and success states always pair an icon with clear, plain-language text (no jargon, no stack traces shown to end users).
- Toasts/snackbars appear in a consistent position (bottom-center or bottom-right) across all tools, auto-dismiss after 4–6 seconds, but remain dismissible and pausable on hover/focus.

---

## 10. Light & Dark Mode

### 10.1 Requirements

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

### 10.2 Contrast

- All text/background combinations must meet **WCAG 2.1 AA**: 4.5:1 for normal text, 3:1 for large text (≥24px or 19px bold).
- Interactive/UI elements (borders, icons conveying state) must meet **3:1** minimum contrast against adjacent colors.
- Never disable or override the OS/browser focus outline without replacing it with an equally visible custom one (see §10.4).
- Test every theme combination with an automated contrast checker before shipping.

### 10.3 Never Rely on Color Alone

Any state or meaning conveyed by color (errors, success, links, active tabs) must have a second, non-color signal: an icon, underline, weight change, or text label. This protects colorblind users (~8% of men) and anyone in poor viewing conditions.

### 10.4 Focus States

- All interactive elements must have a clearly visible `:focus-visible` style — a 2px accent-colored outline with at least 2px offset from the element:
  ```css
  :focus-visible {
    outline: 2px solid var(--color-accent);
    outline-offset: 2px;
  }
  ```
- Never use `outline: none` without a compliant replacement.

---

## 11. Accessibility (WCAG 2.1 AA Minimum)

Accessibility is mandatory for every tool shipped in this ecosystem — not an enhancement.

### 11.1 Structure & Semantics

- Use real, semantic HTML elements (`<button>`, `<nav>`, `<main>`, `<label>`, `<table>`) before reaching for `<div>` + ARIA. ARIA is a supplement, not a substitute.
- One `<h1>` per page. Heading levels must be sequential (no skipping from `<h1>` to `<h3>`).
- All images/icons that convey meaning have descriptive `alt` text; purely decorative images use `alt=""`.
- Landmarks (`<header>`, `<main>`, `<footer>`, `<nav>`) are used so screen reader users can navigate by region.

### 11.2 Keyboard Access

- **Every interactive element must be reachable and operable via keyboard alone** — tab order follows visual/logical order.
- No keyboard traps. Modals trap focus intentionally while open and return focus to the triggering element on close.
- All custom components (dropdowns, tabs, sliders) follow WAI-ARIA Authoring Practices for expected keyboard behavior (Enter/Space to activate, Escape to close, arrow keys within composite widgets).
- Provide a "skip to main content" link as the first focusable element on every page.

### 11.3 Screen Readers

- Dynamic content changes (results, validation messages, loading states) are announced via `aria-live="polite"` (or `assertive` for critical errors) regions.
- Form controls are programmatically associated with their labels (`<label for>` or `aria-labelledby`).
- Icon-only buttons always include an accessible name via `aria-label` or visually-hidden text.

### 11.4 Motion & Sensory Considerations

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

### 11.5 Touch & Pointer Devices

- Minimum interactive target size: **44×44px** (WCAG 2.5.5, Apple/Google guidelines), with at least 8px spacing between adjacent targets.
- Support both mouse and touch equally — no hover-only functionality. Anything triggered by `:hover` must have a touch/click/focus equivalent.
- Design for both portrait and landscape orientations; do not lock orientation.
- Test on real trackpads, touchscreens, and with keyboard-only navigation, not just mouse and desktop.

### 11.6 Responsive & Device Range

- Mobile-first responsive design; layouts must work from 320px width up through large desktop/ultra-wide displays.
- Text and controls must remain usable when the browser is zoomed to 200% (WCAG 1.4.4) without loss of content or function, and reflow properly up to 400% zoom without horizontal scrolling (WCAG 1.4.10).
- Support user font-size overrides — avoid fixed `px` heights on text containers that would clip enlarged text; prefer `rem`/`em` for typography-related sizing.

---

## 12. Performance as a UX Requirement

Because simplicity includes speed:

- No tool should ship a JS framework/bundle heavier than necessary for its function. Prefer vanilla JS/HTML/CSS or lightweight libraries.
- No unnecessary third-party scripts, ads, or trackers. If analytics are used, they must be privacy-respecting and disclosed.
- Target: first meaningful paint under 1 second on a median mobile connection.
- All tools must function fully offline-first where feasible (static, client-side processing) — this is both a performance and a privacy benefit central to the "serverless tools" mission.

---

## 13. Voice & Content

- Plain language always. No jargon, no marketing fluff, no exclamation points unless truly warranted.
- UI copy is short, direct, and action-oriented ("Copy result", not "Click here to copy your result to the clipboard!").
- Error messages explain what happened and how to fix it — never just "Something went wrong."
- No dark-pattern copy: no guilt-tripping cancel buttons, no countdown timers, no fake scarcity.

---

## 14. Checklist Before Shipping a New Tool

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
- [ ] No footers, attribution, or project links on the main app page — only tool functionality
- [ ] Non-app pages (about, help) include attribution (tool name, Free Open Tools mark, link to benjaminbarlow.com)
- [ ] (Mini tools) Single HTML file, no build step, no separate repo
- [ ] (Mini tools) Centered layout ≤600px, no fixed toolbar or properties panel

---

## 15. Mini Tools

Mini tools are single-page utilities that serve a narrow, specific purpose (URL encoding, base64 conversion, JSON formatting, etc.). They differ from full applications in scale and complexity but must still feel like part of the Free Open Tools ecosystem.

### 15.1 Scope

- A mini tool is a **single HTML file** with inline CSS and inline JavaScript. No build step, no repo, no separate deployment.
- Each mini tool lives in its own directory under the `freeopentools` domain (e.g., `/url-encode-decode/index.html`).
- Mini tools do not have separate GitHub repos. Source is linked from the page itself or from the ecosystem GitHub.
- A mini tool solves exactly one problem. If a tool needs multiple related functions (encode + decode), they coexist on one page.

### 15.2 Page Architecture

Mini tools use a simplified page structure — no toolbar, no properties panel, no full-screen canvas. The page is a centered single-column layout:

```
┌──────────────────────────────┐
│         Page header          │  -- small, one-line title + icon
│  ─────────────────────────── │
│                              │
│       Tool functionality     │  -- input(s), actions, output
│                              │
│  ─────────────────────────── │
│   Footer (ecosystem link)    │  -- attribution (see §9.4)
└──────────────────────────────┘
```

- **Page header**: A small title with an inline SVG icon (same 16px/700 style as the app logo, §7.8), plus a back link to the ecosystem landing page.
- **Tool area**: The core functionality — inputs, buttons, outputs. Must follow the ecosystem's spacing, color, and typography tokens.
- **Footer**: Attribution footer (as required by §9.4 for non-app pages).

### 15.3 Styling & Layout

Mini tools do not get the fixed toolbar or properties panel. Instead:

- **Width**: Content is centered at `max-width: 600px` (or up to 720px for tools with side-by-side panes).
- **Padding**: `var(--space-5)` (24px) on each side at desktop, `var(--space-3)` (12px) on mobile.
- **Header**: The page title is `font-size: var(--text-xl)` (22px), `font-weight: 700`, placed above the tool area. An optional back link (`← Tools`) sits above the title in `var(--text-sm)` muted color.
- **Inputs & outputs**: Follow the same input conventions as §7.14 — `16px` minimum font, `--radius-sm` borders, `--color-border-input` border, accent focus ring.
- **Buttons**: Use `.tool-btn` conventions from §7.10 (44×44px minimum) for icon buttons, or standard form buttons (`padding: var(--space-2) var(--space-4)`, `font-size: 16px`, `border-radius: var(--radius-sm)`) for text-labeled actions.
- **Primary action button**: Follows §7.12 (solid accent background, white text).
- **No toolbar chrome**: Mini tools are lightweight by design — no fixed-position headers, no scrollable toolbars, no z-index layering battles.

### 15.4 Input/Output Patterns

Mini tools typically have one or more of:

- **Textarea input**: Full-width, minimum 100px tall, monospace font suitable for code/text data.
  ```css
  .mini-input {
      width: 100%;
      min-height: 100px;
      padding: var(--space-3);
      border: 1px solid var(--color-border-input);
      border-radius: var(--radius-sm);
      background: var(--color-input-bg);
      color: var(--color-text);
      font-family: var(--font-mono);
      font-size: 16px;
      line-height: 1.5;
      resize: vertical;
  }
  .mini-input:focus {
      border-color: var(--color-accent);
      outline: none;
      box-shadow: 0 0 0 2px var(--color-accent-soft);
  }
  ```
- **Action buttons**: Grouped horizontally with `gap: var(--space-2)`.
- **Output area**: Read-only display, same styling as the input, with a copy button if appropriate.
- **Copy button**: A small button next to the output labeled "Copy" or with a clipboard SVG icon.

### 15.5 Responsive

- At `max-width: 480px`, action buttons stack vertically (full-width) instead of side-by-side.
- The textarea should remain at least 100px tall but can grow with `resize: vertical`.

### 15.6 Attribution

Mini tools are non-app pages (as defined in §1.5). They must include the footer attribution: tool name, Free Open Tools mark, and a link to `benjaminbarlow.com` (see §9.4).

---

## 16. About This Guide

This style guide is the single source of truth for the Free Open Tools ecosystem. It exists to ensure that every tool — whether a full application or a single-page utility — feels like it belongs to the same family.

- **Maintained by**: Benjamin Barlow
- **Inspired by**: The diagram editor's implementation (the first tool in the ecosystem)
- **Version**: 2.0
- **Feedback**: Reach out at [bflbarlow@gmail.com](mailto:bflbarlow@gmail.com)

---

*This is a living document. Improvements should raise the bar on simplicity and accessibility — never lower it for the sake of novelty.*
