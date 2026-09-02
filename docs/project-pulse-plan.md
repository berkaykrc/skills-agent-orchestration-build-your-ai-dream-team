# Project Pulse Dashboard Implementation Plan

## Summary

Build Mona's **Project Pulse** as a small, dependency-free static dashboard for contributors. The dashboard will present multiple project cards containing each project's name, owner, status, recent activity, priority or risk level, and a short contributor-friendly summary.

The implementation will use HTML, CSS, JSON, and inline browser JavaScript only. No package manager, framework, build step, or additional dependency is required.

## Implementation steps

### 1. Confirm requirements and establish contracts

**Owner:** Planner, coordinated by Orchestrator

Establish the implementation phases, file ownership, design constraints, data contract, launch behavior, dependencies, and validation criteria.

The contracts are:

- `app/index.html` is the dashboard entry point.
- `app/styles.css` contains all dashboard styling.
- `app/project-data.json` contains a top-level `projects` array.
- Every project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Each project may also include a short `summary` field.
- The page loads `project-data.json` through a relative URL and renders visible cards with the `project-card` class.
- `.vscode/launch.json` runs `python3 -m http.server 5500` from the `app/` directory and opens `index.html`.

### 2. Define the visual and accessibility direction

**Owner:** Designer  
**Files:** `app/index.html`, `app/styles.css` (design guidance)

The Designer provides the Orchestrator and Coder with a UI handoff covering:

- A clear page title containing **Project Pulse**.
- A dashboard header that explains the page's purpose.
- A responsive card grid for desktop, tablet, and mobile viewports.
- A consistent hierarchy: project name, summary, owner, status, recent activity, and priority.
- Status and priority badges that use text as well as color.
- Deterministic selectors including `.dashboard` and `.project-card`.
- Rounded corners, shadows, contrast, readable typography, and clear spacing.
- Semantic landmarks, heading hierarchy, labels, keyboard-friendly controls if any are introduced, and visible focus states.
- Understandable loading, empty, and error states for screen-reader and keyboard users.

The Designer does not introduce a framework, external asset dependency, or unsupported interaction.

### 3. Create the project data source

**Owner:** Coder  
**File:** `app/project-data.json`

Create valid JSON with a top-level `projects` array containing several representative projects. Each object includes `name`, `owner`, `status`, `recentActivity`, `priority`, and, where needed, `summary`.

Use realistic deterministic sample values. Keep statuses and priorities consistent enough for predictable styling, while allowing unfamiliar values to remain readable. The file must contain JSON only: no comments or trailing commas.

### 4. Implement the dashboard document and rendering behavior

**Owner:** Coder  
**File:** `app/index.html`

Create the dashboard entry point with:

- **Project Pulse** in both the document title and visible page heading.
- A stylesheet reference to `styles.css`.
- A relative reference to `project-data.json`.
- Semantic `header`, `main`, and labelled project-section structure.
- A project-card container using the `.dashboard` hook.
- Inline JavaScript that fetches the JSON and renders one visible `.project-card` per project.
- Explicit rendering of `status`, `recentActivity`, and `priority`, plus owner and summary content.
- Loading, empty, malformed-data, and request-failure states.
- Safe DOM text insertion rather than inserting untrusted values as raw HTML.

Validate that the response contains a `projects` array and show a clear user-facing error when the contract is not met. Do not silently show an empty dashboard when data cannot be loaded.

### 5. Implement the visual system

**Owner:** Coder, guided by Designer  
**File:** `app/styles.css`

Implement:

- `.dashboard` and `.project-card` layout hooks.
- A responsive grid or flex layout.
- Readable spacing and typography.
- Distinct, high-contrast status and priority treatments.
- Rounded cards using `border-radius`.
- Depth using `box-shadow`.
- Narrow-screen behavior without horizontal scrolling.
- Visible focus styles and usable text sizing.
- Loading, empty, and error-state styling.
- Reduced-motion-friendly behavior if transitions or animations are used.

Keep the CSS self-contained and independent of external fonts, libraries, or network resources.

### 6. Add the VS Code launch configuration

**Owner:** Coder  
**File:** `.vscode/launch.json`

Create strict JSON with no comments. Add a configuration named exactly `Run Project Pulse Dashboard` that:

- Runs `python3 -m http.server 5500`.
- Sets `cwd` to `${workspaceFolder}/app`.
- Uses a server-ready action to open `http://localhost:%s/index.html`.
- Opens the dashboard entry point rather than the `app/` directory root.
- Preserves the existing `.vscode/tasks.json` behavior.

### 7. Integrate and review the complete result

**Owner:** Orchestrator with Designer and Coder  
**Files reviewed:** `app/index.html`, `app/styles.css`, `app/project-data.json`, `.vscode/launch.json`

Review that HTML class and data-field names match the CSS and JSON contracts, the JSON path works from the `app/` working directory, the launch URL includes `/index.html`, all required project information is visible, and existing workspace configuration remains intact.

## File assignments

| File | Primary owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic page structure, data loading, card rendering, and UI states |
| `app/styles.css` | Coder, guided by Designer | Responsive layout, visual hierarchy, accessibility, badges, cards, and states |
| `app/project-data.json` | Coder | Deterministic project data using the required schema |
| `.vscode/launch.json` | Coder | Strict JSON launch configuration for the local static server |
| `.vscode/tasks.json` | Existing configuration; do not modify | Preserve automatic Copilot CLI terminal startup |

The Designer owns the visual and accessibility decisions but does not need a new repository file. The Orchestrator owns coordination, explicit file scopes, integration, and final review.

## Dependencies

1. Planner requirements and file contracts must be available before implementation.
2. Designer's information hierarchy, selector contract, and accessibility direction must be agreed before final HTML and CSS integration.
3. `app/project-data.json` must define the schema before rendering code is finalized.
4. `app/index.html` depends on the JSON shape and CSS hooks.
5. `.vscode/launch.json` depends on the final entry-point path and server requirements, but can be authored independently once those paths are fixed.
6. Integration review and browser validation occur after all four implementation files exist.

## Parallel work decisions

After requirements are confirmed, these tasks can run in parallel:

- Designer defines layout, accessibility, responsive behavior, and selector conventions.
- Coder prepares representative `app/project-data.json` content.
- Coder drafts `.vscode/launch.json`, since its command, working directory, and URL are specified.
- Coder scaffolds semantic HTML using the agreed data and selector contracts.

These tasks must remain sequential:

1. Planner requirements and contracts before implementation.
2. Designer's UI and data-field handoff before final HTML/CSS integration.
3. JSON schema agreement before finalizing data rendering.
4. HTML, CSS, and JSON completion before integrated browser review.
5. `launch.json` completion before testing the Run and Debug flow.
6. Final validation after all files are integrated.

If the Designer changes a selector or data-presentation decision after coding begins, the Orchestrator coordinates the change explicitly rather than allowing concurrent edits to the same file.

## Edge cases and risks

- **Direct file opening:** `file://` may prevent `fetch()` from reading JSON; validation must use the configured local server.
- **Missing or malformed JSON:** Show a clear error state instead of leaving a blank dashboard.
- **Missing `projects` key:** Treat it as invalid data and report the expected schema.
- **Empty project list:** Show an accessible empty-state message.
- **Incomplete project records:** Use explicit fallback text or omit invalid cards with a visible data warning; do not silently render misleading values.
- **Unknown status or priority:** Preserve the text and use a neutral visual treatment.
- **Long content:** Ensure names and activity descriptions wrap without breaking the grid or causing horizontal scrolling.
- **Narrow viewports:** Collapse the grid to one column while preserving readable spacing.
- **Color contrast:** Badge text must remain legible independently of color.
- **Port conflicts:** If port `5500` is occupied, stop the conflicting process or update the launch configuration and URL together.
- **Launch syntax:** Keep `launch.json` strict JSON without comments.
- **Existing workspace behavior:** Do not alter `.vscode/tasks.json`, dev-container settings, or Copilot CLI startup behavior.

## Validation expectations

### Automated and structural validation

Run the repository's existing checks after implementation:

```bash
python3 -m json.tool app/project-data.json
python3 -m json.tool .vscode/launch.json
bash scripts/validate-exercise.sh
```

The implementation should satisfy checks that confirm:

- All four assigned files exist.
- `app/index.html` contains **Project Pulse**, references `styles.css` and `project-data.json`, contains `project-card`, and renders or references `status`, `recentActivity`, and `priority`.
- `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.
- `app/project-data.json` parses as valid JSON, has a top-level `projects` key, and includes required project fields.
- `.vscode/launch.json` parses as valid JSON, contains `Run Project Pulse Dashboard`, and includes `index.html`.

### Functional validation

Use **Run Project Pulse Dashboard** in VS Code:

1. Start the configuration from Run and Debug.
2. Confirm the server starts on port `5500`.
3. Confirm the browser opens `http://localhost:5500/index.html`.
4. Confirm the Project Pulse dashboard appears instead of a directory listing.
5. Confirm multiple project cards are visible.
6. Confirm every card displays the project name, owner, status, recent activity, priority, and summary where provided.
7. Confirm the layout remains usable at desktop and narrow viewport widths.
8. Confirm loading, empty, and error states are understandable.
9. Stop the preview server after testing.

No blocking questions remain because the brief defines the required files, data keys, server command, port, working directory, and launch URL. Exact palette, sample projects, neutral unknown-value treatment, and server-ready regular expression remain implementation choices within these constraints.
