# Project Pulse Dashboard — Implementation Plan

## Summary

Project Pulse is a lightweight static frontend dashboard for Mona's team. It gives contributors a quick visual overview of active projects — showing each project's name, owner, status, recent activity, and priority level. The dashboard is built as a small static app (`app/index.html`, `app/styles.css`, `app/project-data.json`) with a VS Code launch configuration (`.vscode/launch.json`) so learners can preview it locally with one click.

---

## Ordered Implementation Steps

### Step 1 — Define the project data

**Files:** `app/project-data.json`
**Owner:** Coder

Create a JSON file with a top-level `"projects"` array. Each object in the array represents a sample project and includes:

- `name` — project display name
- `owner` — contributor responsible
- `status` — e.g. "On Track", "At Risk", "Complete"
- `recentActivity` — short description of latest activity
- `priority` — e.g. "High", "Medium", "Low"

Include 3–5 sample projects with varied statuses and priorities to exercise all visual states in the dashboard.

---

### Step 2 — Design the stylesheet

**Files:** `app/styles.css`
**Owner:** Designer

Create a polished stylesheet that includes:

- A `.dashboard` selector for the overall page/container layout (max-width, centering, padding).
- A `.project-card` selector with `border-radius`, `box-shadow`, padding, and margin for card styling.
- Status badge styles (color-coded by status value).
- Priority indicator styles (visual weight for High/Medium/Low).
- Responsive layout using CSS Grid or Flexbox so cards reflow on narrow screens.
- Clear typography: system font stack, readable sizes, appropriate heading hierarchy.
- Sufficient color contrast (WCAG AA minimum) for text and badges.
- Subtle hover/focus states for interactive affordance.

---

### Step 3 — Build the HTML dashboard page

**Files:** `app/index.html`
**Owner:** Coder (implementation), with structural/semantic guidance from Designer

Create a semantic HTML page that:

- Sets the document title to "Project Pulse".
- Links `styles.css` in the `<head>`.
- Contains a `<main class="dashboard">` wrapper.
- Includes a visible `<h1>` with "Project Pulse".
- Uses JavaScript (`fetch` or inline `<script>`) to load `project-data.json` at runtime and render one `.project-card` element per project.
- Displays each project's `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Uses semantic elements: `<article>` for cards, `<span>` or `<p>` for metadata, appropriate headings.
- Includes ARIA attributes where needed (e.g., `role="list"` on the card container if using `<div>`s, `aria-label` on status badges).

---

### Step 4 — Create the VS Code launch configuration

**Files:** `.vscode/launch.json`
**Owner:** Coder

Create a strict JSON file (no comments) with a launch configuration named **"Run Project Pulse Dashboard"** that:

- Uses a shell-based launch type to start a local server.
- Runs `python3 -m http.server 5500` as the server command.
- Sets `"cwd"` to `"${workspaceFolder}/app"`.
- Includes a `serverReadyAction` that opens `http://localhost:%s/index.html` in the browser when the port is detected.
- Ensures the browser opens `index.html` directly, not a directory listing.

---

## Designer Responsibilities

| Concern | Detail |
|---------|--------|
| Layout | Responsive grid/flexbox card layout within `.dashboard` |
| Visual polish | `border-radius`, `box-shadow`, spacing, contrast |
| Typography | System font stack, heading sizes, line-height |
| Accessibility | Color contrast ≥ 4.5:1, semantic HTML guidance, ARIA guidance |
| CSS hooks | `.dashboard`, `.project-card`, status/priority utility classes |
| Responsive | Cards stack single-column below 600 px, multi-column above |

**Files owned:** `app/styles.css` (full ownership), `app/index.html` (structural/semantic guidance only — Designer does not write the final file).

---

## Coder Responsibilities

| Concern | Detail |
|---------|--------|
| Data structure | Valid JSON with top-level `"projects"` key |
| App logic | Fetch JSON, render cards dynamically, error handling |
| HTML implementation | Build the complete `index.html` following Designer guidance |
| Launch config | Strict JSON, correct cwd, serverReadyAction |
| Validation | Confirm JSON parses, page loads without console errors |

**Files owned:** `app/index.html` (implementation), `app/project-data.json`, `.vscode/launch.json`.

---

## Dependencies Between Steps

```
Step 1 (project-data.json)  ─┐
                              ├──▸ Step 3 (index.html) — needs data schema + CSS classes
Step 2 (styles.css)         ─┘

Step 4 (launch.json) — independent of all other steps
```

- **Step 3 depends on Steps 1 and 2.** The HTML must reference the JSON field names and use the CSS class names defined in Steps 1 and 2.
- **Step 4 has no dependencies.** It only needs to know the target directory (`app/`) and filename (`index.html`), which are fixed by convention.

---

## Parallel Work Decisions

| Parallel group | Steps | Rationale |
|----------------|-------|-----------|
| **Group A** | Step 1 + Step 2 + Step 4 | Non-overlapping files (`project-data.json`, `styles.css`, `launch.json`). No data dependencies between them. |
| **Sequential** | Step 3 runs after Group A | `index.html` must integrate the data schema from Step 1 and the CSS classes from Step 2. |

Steps 1, 2, and 4 can all execute simultaneously. Step 3 must wait until all three are complete.

---

## Validation Expectations

1. **JSON validity** — `python3 -m json.tool app/project-data.json` exits 0; top-level key is `"projects"`.
2. **Launch config validity** — `python3 -m json.tool .vscode/launch.json` exits 0; contains `"Run Project Pulse Dashboard"`.
3. **Browser rendering** — Open `app/index.html` via the launch config; visible cards appear (not a directory listing).
4. **Data renders** — Each project card shows `name`, `owner`, `status`, `recentActivity`, and `priority` values from the JSON.
5. **CSS selectors present** — `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
6. **Responsive layout** — Resize browser below 600 px; cards reflow to single column without horizontal scroll.
7. **Accessibility** — Run browser DevTools Accessibility audit or Lighthouse; no critical violations for contrast or missing labels.
8. **No console errors** — Browser console is clean when the page loads.

---

## Edge Cases

| Edge case | Mitigation |
|-----------|-----------|
| `project-data.json` fails to load (network error or wrong path) | Show a user-friendly error message in the dashboard area instead of a blank page. |
| JSON is malformed | Wrap `JSON.parse` / `fetch` in try-catch; display error state. |
| Empty `projects` array | Show "No projects found" placeholder. |
| Long project names or descriptions | Use `overflow-wrap: break-word` and `text-overflow: ellipsis` where appropriate. |
| Browser doesn't support `fetch` | Acceptable to target modern browsers (ES6+); document this assumption. |
| Color-only status indicators | Pair color with text labels or icons so color-blind users can distinguish states. |
| `.vscode/launch.json` already exists | Overwrite with the correct content (template repo does not track this file). |
| Port 5500 already in use | Document that the learner should stop other servers; no programmatic mitigation needed for this exercise. |

---

## Open Questions

1. **Exact sample project content** — Should the sample projects reference real GitHub-related themes (e.g., "Octocat API", "Mona's Docs") or generic placeholders? (Recommendation: use Mona-themed names for story consistency.)
2. **Dark mode** — Should the stylesheet include a `prefers-color-scheme: dark` variant? (Recommendation: not required for MVP; note as future enhancement.)
3. **Animations** — Should cards animate in on load? (Recommendation: optional; keep simple for accessibility and determinism.)
