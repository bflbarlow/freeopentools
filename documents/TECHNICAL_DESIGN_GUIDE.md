# Free Open Tools — Technical Design Guide

**Version 1.0**

This document is the engineering companion to `STYLE_GUIDE.md`. Where the Style Guide governs *look, feel, and UX behavior*, this guide governs *code structure, architecture, and engineering conventions*. Together they are the two source-of-truth documents every tool in the ecosystem must conform to.

This guide is derived from an audit of the four existing applications — **diagram**, **layout**, **markdown**, and **database** — comparing their actual implementations to find where they agree, where they diverge, and what the correct converged standard should be. Those four apps are the reference corpus; this document is the destination all four (and every future app) should converge toward.

**Audience:** Any developer or coding agent starting work on a Free Open Tools application — whether building a new tool from scratch or bringing an existing tool into conformance.

---

## 1. Purpose & Scope

### 1.1 Why This Document Exists

The four existing apps were each built independently, at different times, with different instincts. An audit reveals real divergence in things that should never diverge:

| Concern | diagram | layout | markdown | database |
|---|---|---|---|---|
| Theme localStorage key | `diagram-theme` | `theme` | `markdown-theme` | `database-theme` |
| Other storage key prefix | `diagram-*` | `lp-*` | `markdown-*` | `db-builder-*` / `database-*` |
| App logic location | `app.js` (separate file) | inline `<script>` in `index.html` | inline `<script>` in `index.html` | `app.js` (separate file) |
| CSS location | `styles.css` (separate file) | inline `<style>` in `index.html` | inline `<style>` in `index.html` | inline `<style>` in `index.html` |
| Central state object | `S` | `state` | none (scattered `var`s) | scattered `var`s, no single object |
| IIFE wrapper | yes | yes | yes (partial) | yes |
| Error handling (`try/catch` count) | 5 | 4 | 0 | 54 |
| Third-party libs | vendored locally (`vendor/*.min.js`) | none | vendored locally (`marked.min.js`) | loaded from CDN (`jsdelivr`) |
| Undo function names | `pushUndo()` / `undo()` / `redo()` | `pushUndo()` (inline) / `undo()` / `redo()` | n/a | ad hoc, string-based undo log |

None of these divergences are functionally motivated — they are accidents of independent development. This guide exists to eliminate them going forward, and to give any developer or agent a single mental model that transfers cleanly between every app in the suite.

### 1.2 Relationship to STYLE_GUIDE.md

- **STYLE_GUIDE.md** — colors, typography, spacing, the App Toolbar, the Properties Pane, accessibility, voice/content. Answers "how should this look and behave to the user?"
- **TECHNICAL_DESIGN_GUIDE.md** (this document) — file layout, state management, module boundaries, naming conventions, persistence, error handling, build/deploy. Answers "how should this be built?"

Both documents are binding. Where either is silent, prefer the simpler, more boring solution — see §2.

### 1.3 Non-Goals

This guide does **not** prescribe a JavaScript framework, bundler, or package manager. Per the Style Guide's performance principles (§12) and the ecosystem's "serverless tools" mission, **every app ships as static files with zero build step.** This is a hard constraint, not a starting preference — see §3.

---

## 2. Core Engineering Principles

These mirror and extend the Style Guide's Core Principles (§1) from a technical angle.

1. **Boring technology wins.** Vanilla JS, vanilla CSS, vanilla HTML. No framework, no transpiler, no bundler, no package manager, no `node_modules`. If a feature can be built with a `<script>` tag and a `for` loop, that is the correct implementation.
2. **One file you can read top to bottom.** `app.js` should be readable in a single sitting by a new contributor. Prefer long, flat, well-commented files over deeply nested module graphs. See §4 for the internal organization that keeps a large single file navigable.
3. **State lives in one place.** Every app has exactly one authoritative state object. UI is a projection of state, never a second source of truth. See §5.
4. **No silent failures.** Every `localStorage`, `JSON.parse`, file I/O, and third-party library call is wrapped in `try/catch` with a visible fallback. See §9.
5. **Everything works offline, from a single directory, opened as a static file.** No server required for local development (`open index.html` must work). No login, no backend, no database server. Client-side persistence only (`localStorage`, in-memory, or client-side file export/import).
6. **Copy-paste-able architecture.** A developer should be able to look at `diagram/app.js` and `database/app.js` side by side and recognize the same skeleton: same section order, same naming patterns, same lifecycle. Consistency of *shape*, even when the *content* differs completely, is the entire point of this document.
7. **Progressive enhancement over defensive complexity.** Ship the simplest thing that meets the Style Guide's accessibility and performance bars. Do not add abstraction for hypothetical future requirements.

---

## 3. Project & File Structure

### 3.1 Standard Directory Layout

Every app — mini tool or full application — lives in its own top-level directory, deployed to its own subdomain. Full applications use this exact file layout:

```
appname/
├── index.html          # The application. Markup only + minimal inline bootstrap.
├── styles.css           # All application CSS. One file, no @import splitting.
├── app.js               # All application logic. One file (see §4 for internal structure).
├── about.html            # Marketing/info page. Self-contained (inline <style>, no app.js dependency).
├── README.md              # User-facing feature docs + developer quick start.
├── CNAME                   # Custom subdomain, e.g. "diagram.freeopentools.com" (GitHub Pages).
└── vendor/                  # Third-party libraries, vendored locally. Never CDN-loaded.
    └── library.min.js
```

This is the **converged standard** — it matches `diagram/` exactly and is the target shape for `layout/`, `markdown/`, and `database/` to migrate toward. Do not invent a new layout per app.

### 3.2 Rules

- **`index.html` contains no `<style>` or business-logic `<script>` blocks.** All CSS goes in `styles.css`; all application logic goes in `app.js`. The only inline `<script>` permitted in `index.html` is the theme-detection bootstrap (Style Guide §10.1) — a 4-line snippet that must run before first paint and therefore cannot live in an external file without introducing a render-blocking synchronous fetch.
  - **Current state:** `layout/index.html` and `markdown/index.html` inline both `<style>` and the entire application in `<script>` tags inside `index.html`. This is the primary structural non-conformance to fix when migrating those apps (see §14).
- **`about.html` is self-contained.** It may inline its own `<style>` block (it is a separate, simple page and does not share `styles.css`'s complexity) but must not load `app.js`. It never contains business logic — only static markup, the theme-detection bootstrap, and a small inline toggle script.
- **`app.js` is a single file.** Do not split into multiple `<script src>` tags or ES modules. A single flat file is easier for a new contributor (human or agent) to grep, and avoids build-step temptation (bundlers, `import`/`export` resolution, CORS-blocked `file://` module loading).
  - **Exception:** vendored third-party libraries (see §3.4) are loaded as separate `<script>` tags before `app.js`, since they are not application code.
- **No `node_modules`, no `package.json`, no bundler config, at the application level.** If a project needs a package manager for a documentation or tooling task, that tooling must live outside the deployed directory and never affect the shipped file set.
- **File naming is fixed.** Always `index.html`, `styles.css`, `app.js`, `about.html`, `README.md` — exactly these names, exactly this casing. A developer moving between apps should never have to check what the CSS file is called.

### 3.3 Mini Tools Exception

Per Style Guide §15, **mini tools** (single-purpose utilities like `url-encode-decode/`) are a deliberately lighter-weight category:

```
mini-tool-name/
└── index.html   # Everything inline: <style> and <script> in one file. No app.js, no styles.css.
```

Mini tools do not get `about.html`, `app.js`, or `styles.css` — the entire tool is one HTML file, because the tool itself is small enough that splitting it into multiple files adds navigation overhead with no benefit. **Do not apply the full-application structure (§3.1) to a mini tool, and do not apply the mini-tool structure to a full application.** Deciding which category a new tool falls into is a scoping decision made before writing any code — see §13.

### 3.4 Third-Party Dependencies

- **Vendor everything locally.** Every third-party library ships as a file inside `vendor/` in the app's own directory, committed to the repo. This is already the correct pattern in `diagram/vendor/` (`html2canvas.min.js`, `jspdf.umd.min.js`) and `markdown/marked.min.js` (which should move into a `vendor/` subdirectory for consistency).
- **Never load from a CDN at runtime.** `database/app.js` currently loads `sql-wasm.js` from `cdn.jsdelivr.net` at runtime — this is a non-conformance to fix (see §14). CDN loading:
  - Breaks the "works offline" principle (§2.5).
  - Introduces a third party as a silent dependency of every page load, contrary to the privacy commitments implied by "free" and "open" (Style Guide §4.4).
  - Is a single point of failure outside the ecosystem's control.
- **Use the smallest, oldest-API-surface version that satisfies the need.** Do not chase the latest major version of a vendored library without a concrete reason.
- **Never use a package manager to fetch vendor files at deploy time.** Download once, commit the file, done.

---

## 4. `app.js` Internal Structure

`app.js` is the single largest and most important file in any Free Open Tools application. Its internal organization must follow a fixed section order so any developer or agent can jump between apps and immediately find what they're looking for.

### 4.1 The IIFE Wrapper

The entire file is wrapped in a single Immediately-Invoked Function Expression in strict mode. This is already consistent across all four existing apps and must remain the standard:

```js
// ===== appname vN =====
(function () {
    'use strict';

    var VERSION = 'N';    // single source of truth for version (see §7.6)

    // ... entire application ...

})();
```

- The `VERSION` constant is the single programmatic source of truth for the app's version string. `saveState()` and the file exporter (§7.6) reference `VERSION` rather than duplicating the version as a magic string elsewhere — keep the version in exactly one place, visible in the opening comment above.
- **No global leakage.** Every variable, function, and constant declared in the file lives inside this closure. The only globals an app is permitted to touch are `window` (for feature detection / vendored library globals like `window.jspdf` or `window.marked`), `document`, and `localStorage`.
- **No ES modules.** Do not use `import`/`export`. The `<script src="app.js">` tag with no `type="module"` is the standard — this keeps `file://` protocol usage working (ES modules are blocked by CORS when opened directly from disk without a server), matching the "just open index.html" requirement (§2.5).

### 4.2 Fixed Section Order

Within the IIFE, organize code into clearly labeled sections, in this exact order. Use a consistent comment-banner style to mark each section (see the exact banner format in §4.3). Not every app needs every section — omit what doesn't apply, but never reorder what you do include.

1. **Version & header comment** — one line identifying the app and version, plus a short "how to debug" note if relevant (diagram's `app.js` models this well: *"Open browser console... If X doesn't work, paste this in console..."*).
2. **`CONFIG`** — a single frozen-in-spirit object holding every magic number the app depends on (thresholds, limits, step sizes, default sizes). See §4.4.
3. **State (`S`)** — the single state object for the entire application. See §5.
4. **DOM refs** — every `document.getElementById` / `document.querySelector` lookup, done once, cached into `var` bindings. See §4.5.
5. **Pure helper functions** — geometry, math, string escaping, ID generation. No DOM access, no state mutation. These should be near the top because everything else depends on them.
6. **Selection / lookup helpers** — functions that find or filter state (`findShape(id)`, `shapeSel()`, etc.).
7. **Persistence** — `saveState()`, `scheduleSave()`, load-on-init logic. See §7.
8. **Undo/redo** — `pushUndo()`, `undo()`, `redo()`. See §8.
9. **Domain operations** — the core CRUD-like functions that mutate state for this specific app's domain objects (`addShape()`, `deleteShape()`, `addConn()`, etc. for diagram; `addElement()`, `updEl()` for layout; `addTable()`, `addColumn()` for database).
10. **Render functions** — pure(ish) functions that read state and update the DOM. See §6.
11. **Event wiring** — all `addEventListener` calls, grouped by UI region (toolbar, canvas/workspace, properties pane, keyboard). See §4.6.
12. **Init** — a single `init()` function, called once at the bottom of the file, that restores persisted state and performs first render.

### 4.3 Section Banner Format

Use this exact comment style to delimit sections — it is greppable (`grep '// =====' app.js` instantly gives a table of contents) and visually distinct from ordinary comments:

```js
// ===== Configuration =====
var CONFIG = { ... };

// ===== State =====
var S = { ... };

// ===== DOM refs =====
var canvas = document.getElementById('canvas');
```

For sub-groups within a large section, use a lighter single-line banner:

```js
// === Undo/Redo ===
function pushUndo() { ... }
```

This two-tier convention (`// ===== Major Section =====` and `// === Subsection ===`) is already used in `diagram/app.js` and should be adopted verbatim by every other app.

### 4.4 `CONFIG` Object

Every tunable constant in the application belongs in one `CONFIG` object near the top of the file — never scattered as inline magic numbers. This is already the correct pattern in `diagram/app.js`:

```js
var CONFIG = {
    connHitPx: 10,          // px threshold for clicking near a connection line
    handleSize: 10,         // half-size of resize handles
    minShapeSize: 20,       // minimum width/height for any shape
    maxUndo: 50,            // max undo history entries
    zoomStep: 0.1,          // zoom increment per wheel/button click
    minZoom: 0.1,
    maxZoom: 5
};
```

Rules:
- Every value has a trailing inline comment explaining its unit and purpose.
- Group related constants together (all zoom values adjacent, all sizing thresholds adjacent).
- Never inline a "magic number" directly into logic (`if (dist < 10)`) — always reference `CONFIG.connHitPx` even if it means a one-time refactor when adding this pattern to an existing app.
- `database/app.js` and `layout/index.html` currently scatter tunables as inline literals or loose top-level `var`s (e.g. `MAX_UNDO`, `GRID` as standalone globals in `layout`) — these should be consolidated into a single `CONFIG` object during conformance work.

### 4.5 DOM Refs

Cache every DOM lookup once, at the top of the file (immediately after `CONFIG` and `S`), into descriptively named `var` bindings:

```js
var canvas       = document.getElementById('canvas');
var shapesLayer  = document.getElementById('shapes-layer');
var propsPanel   = document.getElementById('properties-panel');
var btnCollapse  = document.getElementById('btn-collapse-panel');
```

Rules:
- **Never call `document.getElementById` or `document.querySelector` repeatedly for the same element inside render loops or event handlers.** Look it up once, reuse the reference.
- Name the variable after what it *is*, not where it's used: `propsPanel`, not `rightPanelThing`.
- If an app uses a shorthand query helper (`database/app.js`'s `var $ = function (s) { return document.querySelector(s); }`), that pattern is acceptable but the *result* of every lookup used more than once should still be cached into a named variable rather than re-querying.
- Group DOM ref declarations logically: toolbar refs together, canvas/workspace refs together, properties-pane refs together — mirroring the App Toolbar / Properties Pane / Canvas structure defined in the Style Guide.

### 4.6 Event Wiring Organization

All event listener registration happens in one place, near the bottom of the file (section 11 in §4.2), grouped by UI region with a banner per group:

```js
// ===== Toolbar: tool buttons =====
document.querySelectorAll('[data-tool]').forEach(function (btn) {
    btn.addEventListener('click', function () { ... });
});

// ===== Toolbar: action buttons =====
document.getElementById('btn-undo').addEventListener('click', undo);
document.getElementById('btn-redo').addEventListener('click', redo);

// ===== Canvas: pointer events =====
container.addEventListener('pointerdown', function (e) { ... });
container.addEventListener('pointermove', function (e) { ... });
container.addEventListener('pointerup', function (e) { ... });

// ===== Properties pane: inputs =====
propFill.addEventListener('input', function () { ... });

// ===== Keyboard shortcuts =====
document.addEventListener('keydown', function (e) { ... });
```

- **Use Pointer Events (`pointerdown`/`pointermove`/`pointerup`), not separate mouse and touch handlers.** This is already correct in `diagram/app.js` (with explicit pinch-to-zoom and pointer-capture handling) and should be the standard everywhere a canvas/workspace needs drag or draw interaction — do not write parallel `mousedown`/`touchstart` handlers.
- Delegate events on containers where the list of targets is dynamic (e.g., layer list items, log entries) rather than attaching/detaching listeners on individual re-rendered elements.

---

## 5. State Management

### 5.1 One State Object, Named `S`

Every application has exactly one authoritative state object, declared once near the top of `app.js`, named `S`. This is the single most important convention in this document because it is what lets a developer open any app's `app.js` and immediately know where to look for "what does the app currently think is true."

```js
var S = {
    // Domain data
    shapes: [],
    connections: [],

    // Selection
    selection: [],

    // Interaction mode
    tool: 'select',

    // Viewport
    zoom: 1, panX: 0, panY: 0,

    // Transient interaction flags
    isDragging: false, isResizing: false, isDrawing: false,

    // History
    undoStack: [], redoStack: [],

    // Persisted preferences
    showGrid: true, snapToGrid: true
};
```

**Naming convergence required:**
- `diagram/app.js` already uses `S` — this is the standard, keep it.
- `layout/index.html` uses `state` — rename to `S` when brought into conformance.
- `database/app.js` has no single state object at all (scattered top-level `var`s: `db`, `tables`, `selectedTable`, `activeTab`, `undoStack`, `relationships`, `selectedRowid`, etc.) — these must be consolidated into a single `S` object.
- `markdown/index.html` has minimal state (mostly derived from the DOM/textarea directly) — if/when it grows more interactive state, it should adopt the same `S` convention rather than scattering new top-level `var`s.

### 5.2 What Belongs in `S`

- **Domain data** — the actual content the user is creating (shapes, connections, pages, elements, tables, rows).
- **Selection state** — what is currently selected, always as an array even if the app usually has single-selection (`selection: []`, not `selectedId: null`) so multi-select is never a later breaking migration.
- **Interaction mode** — the active tool/mode (`tool: 'select'`).
- **Viewport state** — zoom, pan, scroll position.
- **Transient interaction flags** — booleans like `isDragging`, `isPanning`, `isResizing` that are true only during an in-progress pointer gesture.
- **History stacks** — `undoStack`, `redoStack` (see §8).
- **User-configurable view preferences that persist** — grid visibility, snap-to-grid, panel width/collapsed state.

### 5.3 What Does NOT Belong in `S`

- **DOM references.** Those live in the DOM refs section (§4.5), not in state.
- **Constants/config.** Those live in `CONFIG` (§4.4), not in state.
- **Derived/computed values that can be recalculated from other state on demand.** Don't cache a computed bounding box in `S` if a pure function can produce it from `S.shapes` whenever needed. Caching derived values in state is a common source of stale-UI bugs.
- **Anything that only matters within a single function call.** Local `var`s inside a function are fine and preferred over polluting `S` with short-lived scratch values.

### 5.4 Mutating State

- State is mutated directly (`S.shapes.push(newShape)`, `s.x = newX`) — this is not a Redux-style immutable-state architecture, and introducing one would violate §2.1 (boring technology wins). Direct mutation is acceptable **because** the render step (§6) is cheap enough to re-run entirely after every mutation; there is no need for fine-grained change detection.
- Every function that mutates `S` in a way the user should be able to undo must call `pushUndo()` (§8) — either right before the mutation (to snapshot the *previous* state) or right after (snapshotting current state before the *next* mutation), but be consistent about which within a single app. `diagram/app.js`'s convention — call `pushUndo()` after a discrete user action completes (e.g., on `pointerup` after a drag, not on every `pointermove`) — is the reference pattern.
- Never mutate `S` inside a render function. Render reads state; it never writes it. This one-way flow (mutate → render) is what keeps a large single-file app comprehensible without a framework enforcing it for you.

### 5.5 IDs

- Every domain object gets a unique, stable `id` string at creation time, stored as a property on the object itself (`shape.id`, `element.id`, `row.id`).
- Use a short, collision-resistant generator. Two acceptable patterns exist in the current corpus — standardize on the **counter-based** pattern for anything that needs simple, sortable, human-debuggable IDs, and the **timestamp+random** pattern only for objects created in rapid succession where a counter might race (rare in single-threaded JS, but connections/edges are the one case where diagram uses it):

  ```js
  // Counter-based (preferred default — diagram's shape IDs, layout's pattern)
  function genId() { return 's' + (S.nextId++); }

  // Timestamp+random (only when needed — diagram's connection IDs)
  function cid() { return 'c' + Date.now() + '-' + Math.random().toString(36).slice(2, 6); }
  ```
- Prefix the ID with a single letter indicating its type (`s` for shape, `c` for connection, `e` for element, `t` for table) so a bare ID string is self-describing and so heterogeneous ID arrays (like a unified "selection" list containing both shapes and connections) can be filtered by `id.charAt(0)` without a separate type field.

---

## 6. Render Model

### 6.1 Full Re-render, Not Incremental Diffing

Every app in this ecosystem is small enough in DOM node count (dozens to low hundreds of elements, not thousands) that a **full teardown-and-rebuild render** on every state change is fast enough and dramatically simpler than incremental DOM diffing. Do not introduce a virtual-DOM library or manual diffing logic — this would violate §2.1.

```js
function render() {
    renderShapes();
    renderConnections();
    renderProperties();
    renderLayerList();
}
```

- One top-level `render()` function calls out to smaller, focused render functions, each responsible for exactly one region of the UI.
- Each sub-render function is responsible for one DOM subtree: clear it (`el.innerHTML = ''` or rebuild via `document.createElement`), then rebuild from current `S`.
- `render()` is called after every state-mutating event completes — not on a timer, not batched, not debounced (debouncing is reserved for *persistence*, see §7.2, not for rendering).

### 6.2 Render Function Rules

- **Render functions read `S`; they never write to `S`.** See §5.4.
- **Render functions are idempotent.** Calling `render()` twice in a row with no state change produces the same DOM both times. This property is what makes "just call `render()` again" a safe default any time you're unsure whether a re-render is needed.
- **Prefer rebuilding a container's children over toggling many individual style properties.** For lists that change shape (shapes on a canvas, rows in a layer list, table rows), clear and rebuild. For a single element whose state toggles (like the theme button's sun/moon icon visibility), targeted `style.display` toggling is fine — the distinction is about *collection* rendering vs. *single-element* state.
- **Use `document.createElement` + `appendChild` for structural DOM, and `innerHTML` for trusted, app-generated SVG/HTML strings only.** Never use `innerHTML` with any string that includes unescaped user-entered text — see §11.2 for the required escaping helper.
- **SVG-in-HTML is the standard rendering technique for canvas-like tools.** `diagram/app.js`'s pattern of building an inline `<svg>` per shape via string templates (`shapeSVG(s)`) or DOM SVG element creation is the reference implementation for anything resembling shapes-on-a-canvas.

### 6.3 Two-Way Sync with Form Controls

When the Properties Pane (or toolbar inputs) both *display* and *edit* the current selection's properties, follow this exact pattern (from `diagram/app.js`'s `syncStyleControlsToShape`):

```js
// On selection change / after any mutation: push state → controls
function syncPropsToShape(s) {
    propFill.value = s.fill;
    propStroke.value = s.stroke;
    propOpacity.value = Math.round(s.opacity * 100);
}

// On user input: push controls → state
propFill.addEventListener('input', function () {
    applyStyleToSelected('fill', propFill.value);
});
```

- The "controls → state" direction happens in an `input` (not `change`) listener so edits apply live as the user drags a slider or types.
- The "state → controls" direction happens inside the render step, every time the render runs, not just on selection change — this guarantees controls never go stale even if state changed through a code path the developer didn't anticipate.
- Never let a control's `input` handler both read *and* immediately overwrite its own `.value` — this causes cursor-jump bugs in text inputs. Only overwrite a control's displayed value from the render path, never from its own change handler.

---

## 7. Persistence

### 7.1 `localStorage` Key Naming — Mandatory Convention

**This is the single highest-priority conformance fix identified by the audit.** Every existing app uses a different, inconsistent key naming scheme:

| App | Theme key (current) | Other keys (current) |
|---|---|---|
| diagram | `diagram-theme` | `diagram-panel-collapsed`, `diagram-panel-width`, `diagram-state` |
| layout | `theme` *(no app prefix at all)* | `lp-zoom`, `lp-panX`, `lp-panY`, `lp-sidebar-collapsed`, `lp-sidebar-width`, `lp-wip-dismissed` |
| markdown | `markdown-theme` | *(none currently)* |
| database | `database-theme` | `db-builder-name`, `db-builder-session` *(inconsistent prefix: `database-` vs `db-builder-`)* |

**Mandatory standard going forward — including for existing apps during conformance work:**

```
<app-id>-<concern>
```

Where `<app-id>` is the tool's own lowercase directory name (`diagram`, `layout`, `markdown`, `database`) used **verbatim and exclusively** — never abbreviated (`lp-`, `db-builder-`), never omitted (`theme` alone).

| Concern | Key pattern | Example (diagram) |
|---|---|---|
| Theme | `<app-id>-theme` | `diagram-theme` |
| Full app state / document | `<app-id>-state` | `diagram-state` |
| Properties Pane collapsed | `<app-id>-panel-collapsed` | `diagram-panel-collapsed` |
| Properties Pane width | `<app-id>-panel-width` | `diagram-panel-width` |
| Viewport zoom | `<app-id>-zoom` | `layout-zoom` |
| Viewport pan X/Y | `<app-id>-panX` / `<app-id>-panY` | `layout-panX` |
| Any dismissible notice | `<app-id>-<notice-name>-dismissed` | `layout-wip-dismissed` |

- **Fix required for `layout`:** rename `theme` → `layout-theme`, `lp-*` → `layout-*` for every key.
- **Fix required for `database`:** rename `db-builder-name` / `db-builder-session` → `database-name` / `database-session` (or fold into `database-state` — see §7.3).
- **Legacy key migration:** when renaming a key that may already be populated in real users' browsers (as `diagram` correctly does for its own predecessor `diagramflow-*` keys), read the old key once on `init()`, migrate its value to the new key, and delete the old key. Never leave both an old and new key active simultaneously long-term.

### 7.2 Save Timing — Debounced, Not Synchronous

Persistence writes are **debounced**, not fired synchronously on every keystroke or pointer move. This is the correct pattern already present in `diagram/app.js`:

```js
var _saveTimer = 0;
function scheduleSave() {
    saveState();                      // save immediately once...
    clearTimeout(_saveTimer);
    _saveTimer = setTimeout(saveState, 300);  // ...then again after settling
}
```

- Call `scheduleSave()` (never `saveState()` directly) from any mutation handler that fires at high frequency (dragging, typing, slider input).
- The 300ms debounce window is the standard default. Tune only with a documented reason.
- `saveState()` itself must be idempotent and cheap — it serializes current `S` (or the relevant subset) to JSON and writes to a single `localStorage` key.

### 7.3 What to Persist

- **Always persist:** theme preference, Properties Pane collapsed/width state, and the user's actual document/content (shapes, pages, tables — whatever the tool's core artifact is).
- **Consider persisting:** last viewport position/zoom, last-used tool, dismissed one-time notices.
- **Never persist:** transient interaction flags (`isDragging`), DOM refs, computed/derived values, undo/redo stacks (history resets on reload — this is expected and acceptable; do not attempt to persist undo history across sessions).
- **Prefer one JSON blob over many scattered keys** for the document/content itself (`diagram-state` containing `{ shapes, connections, canvasW, canvasH, ... }` as one serialized object) — but **use separate keys for independent UI preferences** (theme, panel width) so toggling one preference doesn't require re-parsing/re-writing the entire document blob.

### 7.4 Save/Load Wrapper Pattern

Every read and write to `localStorage` is wrapped in `try/catch`. `localStorage` can throw (private browsing mode, quota exceeded, disabled by browser policy) and a thrown exception here must never crash the app:

```js
function saveState() {
    try {
        localStorage.setItem('appname-state', JSON.stringify({
            shapes: S.shapes,
            connections: S.connections
        }));
    } catch (e) {
        console.error('saveState failed:', e);
        // App continues to function in-memory; user is not blocked.
    }
}

function loadState() {
    try {
        var raw = localStorage.getItem('appname-state');
        if (!raw) return null;
        return JSON.parse(raw);
    } catch (e) {
        console.error('loadState failed:', e);
        return null;   // Fall back to empty/default state — never throw from here.
    }
}
```

- `markdown/index.html` currently has zero `try/catch` blocks anywhere in the file — this must be corrected for any persistence code added to it, per §9.
- A failed load must always fall back to a sane default empty state, never leave `S` partially populated or `undefined`.

### 7.5 File-Based Save/Load (Export/Import)

Tools whose core artifact is a document (diagram, layout) additionally support explicit file save/load via the browser's native download/upload mechanisms — this is a *separate* concern from `localStorage` auto-save, and both should typically coexist:

- **Save to file:** serialize the relevant state slice to JSON, create a `Blob`, trigger a synthetic `<a download>` click, then `URL.revokeObjectURL()` after a short delay (diagram's pattern: `setTimeout(function () { URL.revokeObjectURL(url); }, 1000);`).
- **Load from file:** a hidden `<input type="file">`, triggered programmatically, reads via `FileReader`, `JSON.parse`s the result inside a `try/catch`, and on success replaces the relevant slice of `S` and calls `pushUndo()` + `render()`.
- Exported filenames should be derived from the user's project/document name (sanitized — strip characters outside `[a-z0-9_-]`, case-insensitive) with a sensible fallback (`'untitled'`) when no name is set.

### 7.6 Save File Metadata Conventions

Every exported JSON document must include a standard metadata envelope alongside the domain content. This ensures any save file is self-describing — a developer (or automated tool) opening a `.json` file from any app can immediately identify its origin, version, and age without needing to inspect the app-specific content fields.

**Required metadata fields** — every exported JSON blob must have this exact top-level shape:

```json
{
    "app": "diagram",
    "appVersion": "1.0.0",
    "created": "2024-01-15T10:30:00.000Z",
    "modified": "2024-01-15T14:22:00.000Z",
    "content": { ... }
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `app` | `string` | yes | The `<app-id>` (directory name) of the tool that created the file — never abbreviated, exactly matching §10.3. |
| `appVersion` | `string` | yes | The version string from the app's header comment (§4.1, section 1). Follows `major.minor.patch` semver convention. |
| `created` | `string` (ISO 8601) | yes | Timestamp of first save/export of this document. Set once on initial creation; never updated afterward. Generated via `new Date().toISOString()`. |
| `modified` | `string` (ISO 8601) | yes | Timestamp of the most recent save/export. Updated on every export. Generated via `new Date().toISOString()`. |
| `content` | `object` | yes | The domain-specific document data (e.g., `{ shapes: [], connections: [] }` for diagram). This is the payload that `loadState()` deserializes into `S`. |

**Implementation rules:**

- **`created` is stable.** Once written into a save file, `created` is never changed by subsequent saves. Only `modified` changes on each export. The `created` value in `localStorage` (or wherever auto-save state lives) must be preserved alongside the content — store it as a field inside the auto-save blob (`<app-id>-state`), not in a separate key.
- **Auto-save includes metadata.** The `localStorage` blob at `<app-id>-state` should also include `app`, `appVersion`, `created`, `modified` alongside the content. This means `saveState()` writes these fields on every auto-save; the overhead is negligible and the benefit (every state blob is self-describing, even inside `localStorage`) is significant.
- **On import, validate the envelope.** When loading an exported file (§7.5), check for the presence of `app`, `appVersion`, `created`, `modified`, and `content` keys. If the file has no envelope or the envelope is structurally wrong, treat it as an unknown-format file and fall back to the app's default empty state — but still attempt to load `content` directly if the file *looks* like a bare payload (has expected domain keys like `shapes` at the top level). This provides backward compatibility with files exported before this convention was established.
- **App versioning convention.** The version string lives in a single source of truth: it must be defined as a `VERSION` constant (a `var` declaration) right inside the IIFE, immediately after the opening header comment (§4.1). `saveState()` and the file exporter both reference `VERSION` rather than duplicating the version as a magic string elsewhere in the code.

---

## 8. Undo / Redo

### 8.1 Standard Function Names

Every app that supports undo/redo implements exactly three functions, named exactly this, with this exact signature (no arguments, no return value):

```js
function pushUndo() { ... }   // Call after a discrete, undo-worthy action completes.
function undo() { ... }        // Pop from undo stack, apply, push current to redo stack.
function redo() { ... }        // Pop from redo stack, apply, push current to undo stack.
```

`diagram/app.js` already implements this correctly. `layout/index.html` implements the same three functions but should rename its backing `state.undoStack` object property access pattern to match once `state` is renamed to `S` (§5.1). `database/app.js` implements an ad hoc, SQL-statement-based undo log rather than a state-snapshot stack — see §8.4 for why snapshot-based undo is the required standard.

### 8.2 Snapshot-Based Undo — the Required Strategy

Undo/redo works by storing serialized **snapshots** of the relevant slice of `S`, not by recording and reversing individual operations:

```js
function pushUndo() {
    S.undoStack.push(JSON.stringify({ shapes: S.shapes, connections: S.connections }));
    if (S.undoStack.length > CONFIG.maxUndo) S.undoStack.shift();
    S.redoStack = [];   // Any new action invalidates the redo stack.
    scheduleSave();
}

function undo() {
    if (!S.undoStack.length) return;
    S.redoStack.push(JSON.stringify({ shapes: S.shapes, connections: S.connections }));
    var d = JSON.parse(S.undoStack.pop());
    S.shapes = d.shapes;
    S.connections = d.connections;
    S.selection = [];
    render();
}
```

Why snapshot-based, and not operation-based (recording "insert row X" and writing an inverse "delete row X"):
- **Simplicity.** An inverse function must be hand-written for every single mutation type in the app, and every one of those inverses is a second place the same bug can be introduced. A snapshot stack needs exactly one serialize/deserialize pair, ever.
- **Correctness by construction.** A snapshot restore is trivially correct — it's the exact prior state. An inverse-operation system can drift out of sync with the forward operation as the app evolves, silently corrupting undo behavior in ways that are easy to miss in testing.
- **`database/app.js`'s current approach** (a hand-rolled undo log that reconstructs and re-executes SQL `INSERT`/`DELETE` statements to reverse row edits) is exactly the fragile, per-operation pattern this section warns against, and should be migrated to snapshot-based undo of the relevant table/schema state during conformance work.

### 8.3 What to Snapshot

- Snapshot only the **domain content** the user is editing (shapes/connections, pages/elements, table schema/rows) — never selection state, viewport, or transient flags.
- If the full document is large, snapshot the smallest slice that captures everything a single undo-able action could have touched, but err toward snapshotting more rather than building a fine-grained dependency tracker — see §2.1.

### 8.4 When to Call `pushUndo()`

- Call it once, after a discrete user action **completes** — not on every intermediate event during a continuous gesture. On drag: call on `pointerup`, not on every `pointermove`. On text edit: call on blur/commit, not on every keystroke.
- Every function under "Domain operations" (§4.2, section 9) that creates, deletes, or structurally modifies a domain object should either call `pushUndo()` itself or be called immediately adjacent to a `pushUndo()` call at the event-handler level. Be consistent within an app about which of these two placements you choose.

### 8.5 History Limits

- `CONFIG.maxUndo` bounds the stack (`diagram`'s default of 50 is a reasonable ecosystem-wide default). Oldest entries are dropped (`.shift()`) once the limit is exceeded — never grow unbounded.
- The redo stack is always cleared on any new undo-able action (§8.2) — redo history is only valid immediately after an undo, never after the user has since made a new edit.

---

## 9. Error Handling

### 9.1 Mandatory `try/catch` Boundaries

The audit found wildly inconsistent defensive coding — from 54 `try/catch` blocks in `database/app.js` down to **zero** in `markdown/index.html`. The standard, going forward, is: **every operation that can throw for reasons outside the app's own logic must be wrapped**, specifically:

| Operation | Must wrap because |
|---|---|
| `localStorage.getItem` / `.setItem` | Can throw in private browsing, quota-exceeded, or disabled-storage contexts |
| `JSON.parse` on anything not generated by this app in this session | Malformed/corrupted/foreign data (imported files, legacy storage keys) |
| Any third-party library call (`sql.js`, `marked.js`, `html2canvas`, `jspdf`) | Library internals are outside this app's control; a parse or render failure in a dependency must not crash the host app |
| `FileReader` results before use | User-supplied files may be malformed, wrong type, or truncated |
| Clipboard API calls (`navigator.clipboard.writeText`) | Not available/permitted in all browser contexts |
| Dynamic `<script>` injection / CDN-independent library bootstrapping | Network or parse failure must degrade gracefully, not hard-crash |

Operations that do **not** need a `try/catch`: simple property access/assignment on the app's own `S` object, DOM queries for elements the app itself controls and knows exist, and arithmetic — wrapping these adds noise without protecting against anything real.

### 9.2 Catch Block Contract

Every `catch` block must do at least one of the following — never a silent empty catch:

```js
try {
    localStorage.setItem(key, value);
} catch (e) {
    console.error('saveState failed:', e);   // 1. Log for debuggability
    // 2. Fall back to a safe default / continue without the feature
    // 3. (For user-initiated actions only) surface a visible, non-blocking message
}
```

1. **Log the error** with enough context to diagnose it (`console.error('<what-was-being-attempted>:', e)`), never a bare `console.log(e)` or nothing at all.
2. **Degrade gracefully** — the app keeps working, minus the one feature that failed. A failed auto-save must not stop the user from continuing to edit.
3. **For user-initiated actions** (import, export, a button click) — surface a short, plain-language message via the app's toast/notification pattern (Style Guide §9.5), never a raw stack trace or the word "error" alone.

### 9.3 Fix Required: `markdown/index.html`

`markdown/index.html` has zero `try/catch` blocks. As it grows beyond simple textarea-to-preview rendering (e.g., if it adds `localStorage` persistence or file import), every new persistence or parsing code path added must follow §9.1–9.2 from the start — do not let this gap widen.

---

## 10. Naming Conventions

### 10.1 JavaScript

| Category | Convention | Example |
|---|---|---|
| The state object | `S` (exactly one letter, always capital) | `S.shapes`, `S.selection` |
| Config object | `CONFIG` (all caps) | `CONFIG.maxUndo` |
| Regular variables & functions | `camelCase` | `findShape`, `panelResizeHandle` |
| Constructor-like factory functions | `camelCase`, verb-first (`create`/`add`/`make`) | `addShape()`, not `Shape()` or `newShape()` |
| DOM ref variables | `camelCase`, named after the element's role | `btnUndo`, `propsPanel`, `zoomDisplay` |
| Boolean state flags | `is`/`has`/`show` prefix | `isDragging`, `showGrid`, `hasSelection` |
| Event handler functions (named, not inline) | `on` + PascalCase description, only when extracted from inline | `onThemeToggleClick` — but prefer inline anonymous functions for one-off listeners (see §4.6); only extract to a named function when reused in multiple places |
| ID prefix per object type | single lowercase letter + counter/timestamp | `s1`, `s2` (shapes), `c172...` (connections) |
| Private/internal helpers not meant for reuse | leading underscore optional, but prefer just keeping them un-exported (everything is already private inside the IIFE) | `_saveTimer` |

- **Never abbreviate the app's own name in identifiers.** No `lp` (layout), `db` (database, when referring to the *app* rather than the actual database connection variable), or similar — this is exactly the drift that produced the `lp-*` / `db-builder-*` localStorage key inconsistency in §7.1. The one legitimate exception is a variable that names an actual technical concept the abbreviation is standard for (e.g., `var db` holding the actual `sql.js` database connection object is fine — that's not an app-name abbreviation, it's the correct short name for a database handle).
- **Function names are verbs; variable names are nouns.** `deleteShape()` not `shapeDelete()`; `selectedShape` not `selectShape` (for a variable holding the result).

### 10.2 CSS

Follow Style Guide §2.2's exact custom-property names for all shared tokens (`--color-accent`, `--space-3`, `--radius-md`, etc.) — do not invent app-local synonyms for concepts the ecosystem palette already names. Where an app needs a property the shared palette doesn't define (e.g., diagram's `--color-canvas-bg`), name it in the same `--color-<noun>[-<modifier>]` pattern already established, and document it in the app's own CSS comments near `:root`.

- Class names: `kebab-case`, structural/semantic (`.panel-section`, `.tool-btn`, `.shape-btn`) — never presentational (`.blue-button`, `.big-text`).
- IDs: `kebab-case`, used only for elements that are singular per page and looked up directly in JS (`#properties-panel`, `#canvas-container`) — never used purely for styling hooks when a class would do (reserve IDs for JS-addressed elements or true one-per-page landmarks).
- State-driven modifier classes: `.active`, `.selected`, `.hidden`, `.collapsed`, `.locked` — plain adjectives, applied/removed via `classList`, never combined with inline `style.display` for the same concern (pick one mechanism per toggle).

### 10.3 File & Directory Naming

- App directories: single lowercase word, no hyphens, matching the subdomain (`diagram`, `layout`, `markdown`, `database`). Multi-word tool names use hyphens only when unavoidable (mini tools like `url-encode-decode`).
- Every localStorage key, CSS custom property prefix, and any other "identify this app" string in code must use this exact same directory name as its root token — this single string (`<app-id>`) is the one canonical identifier for the app and should never have a second spelling anywhere (see §7.1).

---

## 11. Security & Data Handling

### 11.1 No Backend, No Network Writes

Every app in this ecosystem is client-side only — there is no server component to secure, no authentication, no API keys, no CORS configuration to reason about, because there is no network call that sends user data anywhere. This eliminates an entire category of concerns (session management, CSRF, SQL injection against a real server) by construction. The one exception category is *reading* a vendored static asset — and per §3.4, even those must be local files, not runtime CDN fetches.

### 11.2 XSS Prevention — User Content Into the DOM

Every app that renders user-entered text into the DOM (shape labels, markdown source, database cell values, page element text) must escape it before using `innerHTML`. This is a hard requirement, not a nice-to-have, even though every app here is "just a local tool with no backend" — a maliciously crafted imported file (a `.json` project file, a `.csv` import) is still an untrusted-input vector.

Standard escaping helper — implement this exact function (or equivalent) in every app that uses `innerHTML` with any user-sourced string:

```js
function escHtml(s) {
    return String(s).replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
}
```

- Use `escHtml()` around any user-entered value inserted via `innerHTML` (log messages containing shape names, layer-list labels, rendered table cell values).
- Prefer `textContent` over `innerHTML` whenever the content has no legitimate need to contain markup — this sidesteps the escaping question entirely and is the simpler, preferred choice per §2.1.
- **The one deliberate exception:** `markdown`'s core purpose is rendering user-authored Markdown to HTML — this is an intentional, expected `innerHTML` sink. Even here, the underlying Markdown-to-HTML library (`marked.js`) must be used in its default-escaping configuration; do not disable its built-in HTML sanitization to "support raw HTML in markdown" without a specific, documented reason.
- **Custom SVG shapes** (diagram's custom-shape feature, which accepts pasted raw SVG markup) are a knowingly-elevated-trust feature — the user is pasting markup into their *own local document*, not injecting into a page other people load. This is acceptable specifically because there is no server and no other user who could ever be affected; document this reasoning inline near the feature if it is ever questioned in review.

### 11.3 Imported File Validation

Any file import path (project JSON, CSV, SQLite file) must:
1. Wrap parsing in `try/catch` (§9.1).
2. Validate the parsed shape minimally before assigning it into `S` (check for expected top-level keys; don't trust that an imported "project file" actually matches the current schema — a file from an older app version, or a hand-edited/corrupted file, should fail gracefully into an error toast, not throw mid-render).
3. Never `eval()` or `Function()` construct against imported file contents, ever, under any circumstance.

### 11.4 No Tracking, No Analytics Beacons

Per Style Guide §12, no third-party analytics/trackers. If usage analytics are ever added ecosystem-wide, they must be privacy-respecting, self-hosted or clearly disclosed, and never block or slow the app's core function — this is a product/style decision (Style Guide's domain) but has a technical corollary: any such script must be loaded `async`/`defer` and must never be a dependency of any core feature's code path (the app must work identically with the analytics script blocked by an ad-blocker).

---

## 12. UI Integration Points

This document does not restate the Style Guide's UI specification (App Toolbar §7, Properties Pane §8, Canvas & Workspace §9) — those sections are the binding visual/behavioral contract. This section covers only the *engineering* seams where `app.js` must hook into that markup.

### 12.1 Required DOM IDs

Because the Style Guide's App Toolbar and Properties Pane sections specify exact HTML structure with exact `id`/`class` values, `app.js` can rely on those ids existing without defensive null-checks scattered everywhere — but every DOM ref lookup (§4.5) should still be guarded once, at lookup time, in case a given app has legitimately omitted an optional component (e.g., a tool with no Properties Pane at all has no `#properties-panel` to find):

```js
var propsPanel = document.getElementById('properties-panel');   // may be null — that's fine
if (propsPanel) { /* wire up panel-specific behavior */ }
```

### 12.2 The `render()` ↔ Style Guide Contract

- Selection-driven visibility of Properties Pane sections (`.panel-shape.hidden`, `.panel-conn.hidden`, etc. — Style Guide §8.6) is toggled from the render step (§6), driven by `S.selection`, never hardcoded based on which tool is active.
- The Action Log (Style Guide §8.9) is appended to from domain operations, not from render — call a single `logAction(msg, category)` helper (matching diagram's implementation) directly inside `addShape()`, `deleteShape()`, etc., immediately after the mutation. This keeps the log's content coupled to *actions*, not to render passes (which may run far more often than actions occur).

### 12.3 Theme-Reactive Rendering

Any render logic that reads a CSS custom property at runtime (to draw into an SVG/canvas where CSS can't directly style, e.g., grid line color, selection outline color) must re-read that value from `getComputedStyle` inside a `readTheme()`-style helper, and that helper must be re-invoked from the theme-toggle handler before the next render — this is the pattern already correct in `diagram/app.js`'s `T` object + `readTheme()` function. Never hardcode a color literal for anything that is supposed to track the current theme.

---

## 13. Starting a New Application

Follow these steps, in order, whenever beginning a new Free Open Tools application — this is the concrete "where do I start" answer this whole document exists to provide.

### 13.1 Step 0 — Classify the Tool

Decide, before writing any code:

- **Full application** (needs App Toolbar, Properties Pane, and/or a Canvas/Workspace, has non-trivial ongoing state) → use the full directory structure (§3.1).
- **Mini tool** (one narrow function, single input → single output, no persistent document) → use the mini-tool structure (§3.3) and stop reading the rest of this section; go to Style Guide §15 instead.

### 13.2 Step 1 — Scaffold from the Template

Do not start a full application from a blank file. Start from `/Users/bflbarlow/Websites/freeopentools_template/` (or whatever the current canonical template directory is) and:

1. Copy `index.html`, `styles.css`, `ui.js`/`app.js` skeleton, `README.md` into the new app's directory.
2. Replace every `[APP_ID]`, `[APP_NAME]`, `[APP_TAGLINE]`, `[APP_ICON_SVG]`, `[OBJECT]` placeholder.
3. Set the `localStorage` key prefix to the new `<app-id>` immediately, everywhere, per §7.1 — this is the easiest time to get it right and the hardest thing to fix later once real user data exists under the wrong key.
4. Delete any App Toolbar / Properties Pane sections the tool doesn't need (per the template's `[SECTION_START]`/`[SECTION_END]` markers) rather than commenting them out — dead markup left "just in case" is a Style Guide §1.1 violation (simplicity first).

### 13.3 Step 2 — Establish `CONFIG` and `S` First

Before writing a single render function or event handler, write out:
1. The full `CONFIG` object with every constant you can already anticipate (§4.4).
2. The full `S` object shape with every field the domain needs, even before the logic that populates them exists (§5). Sketching `S`'s shape up front is the single highest-leverage design step — it forces the domain model into the open before any UI code commits to a wrong shape.

### 13.4 Step 3 — Build the Render Skeleton

Write `render()` and its sub-functions (§6) against the `S` shape from Step 2, even with empty/placeholder data — this proves the render contract (state in, DOM out, no mutation) before any interactive complexity is layered on top.

### 13.5 Step 4 — Wire Up Domain Operations, Then Events, Then Persistence, Then Undo

In this order:
1. Domain operations that mutate `S` (§4.2 section 9) — test by calling them from the browser console before any UI triggers them.
2. Event wiring (§4.6) that calls those domain operations.
3. Persistence (§7) — `saveState`/`loadState`, wired into `init()` and `scheduleSave()` calls added to the domain operations from step 1.
4. Undo/redo (§8) — `pushUndo()` calls added last, once the shape of what needs snapshotting is stable; retrofitting undo onto operations that already exist is low-risk because it's purely additive (a `pushUndo()` call and nothing else).

### 13.6 Step 5 — `about.html`

Write `about.html` last, once the tool's actual feature set is stable — it is marketing/documentation copy describing what was actually built, not a spec written in advance. Follow the exact self-contained structure in §3.2 and Style Guide §9.4 for attribution requirements.

---

## 14. Conformance Plan for Existing Applications

This section is the concrete, per-app punch list for bringing `diagram`, `layout`, `markdown`, and `database` into conformance with this guide. **No changes have been made to these projects as part of writing this document** — they remain the reference corpus this guide was derived from. This section is the roadmap for future work, prioritized by risk and impact.

### 14.1 diagram

Closest to conformant already — it is the primary reference implementation this guide's `app.js` structure (§4), state (§5), and persistence (§7) sections are modeled on.

- [ ] **P2:** Consolidate any remaining inline magic numbers into `CONFIG` if found on re-audit.
- [ ] **P3:** No structural changes required otherwise. Use as the reference when other apps are migrated.

### 14.2 layout

- [ ] **P1:** Move all CSS out of the inline `<style>` block in `index.html` into `styles.css` (§3.2).
- [ ] **P1:** Move all application logic out of the inline `<script>` block in `index.html` into `app.js` (§3.2).
- [ ] **P1:** Rename `theme` localStorage key → `layout-theme`; rename all `lp-*` keys → `layout-*` (§7.1), with legacy-key migration on `init()`.
- [ ] **P2:** Rename the state object `state` → `S` (§5.1).
- [ ] **P2:** Consolidate standalone tunable globals (e.g., `GRID`, `MAX_UNDO`) into a single `CONFIG` object (§4.4).
- [ ] **P3:** Adopt the two-tier section-banner comment convention (§4.3) once the file is split out of `index.html` and easier to navigate.

### 14.3 markdown

- [ ] **P1:** Move the inline `<style>` block into `styles.css` (§3.2).
- [ ] **P1:** Move the inline `<script>` block into `app.js` (§3.2).
- [ ] **P1:** Move `marked.min.js` into a `vendor/` subdirectory (§3.4) — it is already vendored locally, just not in the standard location.
- [ ] **P2:** Add `try/catch` boundaries around any current or future `localStorage`/parsing code (§9.1) — currently zero exist in the file.
- [ ] **P3:** If/when persistent document state is added (currently minimal), introduce a proper `S` object and `<app-id>-state` key rather than ad hoc additions.

### 14.4 database

- [ ] **P1:** Stop loading `sql-wasm.js` from `cdn.jsdelivr.net` at runtime; vendor it locally into `vendor/` (§3.4).
- [ ] **P1:** Rename `db-builder-name` / `db-builder-session` keys → `database-name` / `database-session` (or fold into a consolidated `database-state`) (§7.1).
- [ ] **P1:** Move the inline `<style>` block in `index.html` into `styles.css` (§3.2).
- [ ] **P2:** Consolidate the many scattered top-level `var`s (`db`, `tables`, `selectedTable`, `activeTab`, `relationships`, `selectedRowid`, etc.) into a single `S` state object (§5.1).
- [ ] **P2:** Replace the ad hoc SQL-statement-based undo log with snapshot-based undo/redo of table schema and row state (§8.2, §8.4) — this is the largest single behavioral refactor on this list and should be scoped and tested carefully given `database`'s 54 existing `try/catch` blocks suggest prior awareness of fragility in this area.
- [ ] **P3:** Audit the existing 54 `try/catch` blocks against §9.2's catch-block contract (log + degrade + user-facing message where appropriate) — high quantity doesn't guarantee each one currently meets the contract.

### 14.5 Sequencing Recommendation

Do the **P1** items across all four apps first — they are structural (file-splitting, key renaming, de-CDN-ing) and low-risk relative to their conformance impact. Do **P2** items (state object consolidation, undo/redo architecture) one app at a time, with the app's existing README/feature list as the regression checklist. Treat **P3** items opportunistically alongside other feature work on each app.

---

## 15. Checklist Before Shipping a New Tool (Technical)

This checklist complements Style Guide §14 (which covers visual/UX/accessibility conformance) with the engineering-specific items from this document.

- [ ] File structure matches §3.1 exactly (`index.html`, `styles.css`, `app.js`, `about.html`, `README.md`, optional `vendor/`) for full applications; matches §3.3 for mini tools.
- [ ] No inline `<style>` or business-logic `<script>` in `index.html` beyond the theme-detection bootstrap.
- [ ] `app.js` is a single IIFE in strict mode, organized in the fixed section order (§4.2) with two-tier banner comments (§4.3).
- [ ] Exactly one state object, named `S` (§5.1); exactly one config object, named `CONFIG` (§4.4).
- [ ] Every `localStorage` key is prefixed `<app-id>-` using the app's exact directory name, no abbreviations (§7.1).
- [ ] All persistence writes are debounced via `scheduleSave()`, not written synchronously on every input event (§7.2).
- [ ] `pushUndo()` / `undo()` / `redo()` implemented with snapshot-based history, not operation-based (§8.2), if the tool has any undo-able state.
- [ ] Every `localStorage`, `JSON.parse`, third-party library call, and file-read is wrapped in `try/catch` with a logging + graceful-degradation catch block (§9).
- [ ] No third-party library is loaded from a CDN at runtime — everything is vendored into `vendor/` (§3.4).
- [ ] Any user-sourced string rendered via `innerHTML` is passed through `escHtml()` first, or `textContent` is used instead (§11.2).
- [ ] Render functions never mutate state; state mutation never happens inside a render function (§6.2).
- [ ] DOM lookups are cached once into named variables, never repeated inside loops or handlers (§4.5).
- [ ] Pointer Events (not separate mouse/touch handlers) are used for any drag/draw canvas interaction (§4.6).
- [ ] Naming conventions (§10) are followed for all new identifiers — especially: no app-name abbreviations anywhere.

---

## 16. About This Guide

- **Maintained by:** Benjamin Barlow
- **Derived from:** A structural audit of `diagram`, `layout`, `markdown`, and `database` — the four applications live in the Free Open Tools ecosystem at the time of writing.
- **Companion document:** `STYLE_GUIDE.md` (visual, UX, and accessibility standard — equally binding).
- **Version:** 1.0
- **Status:** None of the four reference applications have been modified as part of authoring this document. This guide is the target state; §14 is the tracked path to get each existing app there.

---

*This is a living document. As the four reference apps are brought into conformance (§14) and as new apps are built from this standard (§13), update this guide to reflect any pattern that proves wrong in practice — but changes should converge the ecosystem further, never fork it into a fifth dialect.*
