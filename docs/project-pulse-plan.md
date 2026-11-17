# Project Pulse implementation plan

## Goal and scope

Build Mona's lightweight, static Project Pulse dashboard so contributors can scan active projects, owners, current status, recent activity, priority or risk, and a short contributor-friendly summary. The deliverable is a responsive, accessible card dashboard, its JSON data, and a VS Code launch configuration that serves the `app/` directory and opens `index.html` rather than a directory listing.

This repository is an orchestration exercise: use the custom agents under `.github/agents/` through GitHub Copilot CLI. The Planner defines phases and ownership; the Orchestrator coordinates them; Designer owns visual and accessibility direction; Coder owns the runnable implementation and integration. Keep git operations with the learner.

## Agent responsibilities and file ownership

| Agent | Responsibility | File scope |
| --- | --- | --- |
| **Planner** | Identify requirements, dependencies, phase order, risks, and acceptance checks. This plan is the Planner deliverable; do not implement the app. | `docs/project-pulse-plan.md` |
| **Orchestrator** | Ask Planner before implementation, confirm the shared UI/data contract, assign bounded scopes, coordinate the handoff, and verify integrated outcomes. Does not implement app files. | Coordination and final integration review; no app-file ownership. |
| **Designer** | Set the visual hierarchy, card and badge treatment, spacing, responsive behavior, color/contrast, typography, and accessible interaction/markup guidance. Implement the styling after agreeing on selectors and design contract with Coder. | `app/styles.css` |
| **Coder** | Build semantic HTML, load and render project records, author representative data, and configure a reproducible local preview. Own integration and implementation validation. | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` |

Designer and Coder should agree before implementation on the shared class names (including `.dashboard` and `.project-card`), the card content order, status/priority presentation, and responsive breakpoints. Designer owns CSS to avoid concurrent edits; Coder owns markup and runtime configuration.

## Ordered implementation phases

### 1. Confirm requirements and agree on the UI/data contract

- **Lead:** Orchestrator, with Planner, Designer, and Coder input.
- Confirm the brief's required fields and the expected selectors and content order before parallel implementation.
- Use the top-level `projects` array in `app/project-data.json`. Each record must provide `name`, `owner`, `status`, `recentActivity`, and `priority`; include a concise contributor-friendly summary as well.
- Decide how records load in the browser. Because the page reads JSON, preview it over HTTP rather than relying on `file://` behavior.
- **Dependency:** This shared contract is a prerequisite for the CSS and HTML/data work below.

### 2. Implement the experience and app in parallel

Once the contract is agreed, these scopes are independent and can proceed concurrently:

- **Designer — `app/styles.css`:** Create polished, responsive dashboard styling for the agreed markup. Include `.dashboard` and `.project-card`, clear status badges and priority/risk cues, readable spacing, contrast, rounded corners, and shadows. Provide visible focus treatment and layouts that work on narrow screens.
- **Coder — `app/index.html` and `app/project-data.json`:** Create a semantic page with the exact title **Project Pulse**, reference `styles.css`, load `project-data.json`, and render one visible `.project-card` per project. Show name, owner, status, recent activity, priority, and summary. Include meaningful loading/error feedback if data cannot be loaded. Populate enough representative records to demonstrate active projects and differing statuses/priorities.

The parallel work is safe only after agreeing on selectors and data fields. Coder and Designer should report any contract mismatch to the Orchestrator rather than silently diverging.

### 3. Add the preview launch configuration

- **Owner:** Coder — `.vscode/launch.json`.
- Create strict JSON with a **Run Project Pulse Dashboard** configuration. Use `python3 -m http.server 5500`, set `cwd` to `${workspaceFolder}/app`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`.
- This depends on the page entry point and server-based JSON loading contract from Phase 1. It may be authored alongside the app files, but integration is checked after all deliverables exist.
- Do not add a package manager or runtime dependency for this static app; Python's HTTP server is supplied by the Codespace environment.

### 4. Integrate and validate

- **Owner:** Coder validates implementation; Orchestrator reviews the integrated experience; Designer reviews visual/accessibility outcomes.
- Confirm HTML, CSS, data, and launch settings agree, then run the dashboard through VS Code's **Run Project Pulse Dashboard** configuration.
- Fix integration issues within the responsible file scope and repeat the relevant checks.

## Dependencies and parallel-work decisions

- **No new application/library dependencies:** plain HTML, CSS, JSON, browser JavaScript as needed, and the Codespace's Python 3 HTTP server. Do not introduce a build step or package manifest for this small static app.
- **Contract before concurrent work:** markup selectors, data keys, and content hierarchy must be agreed before Designer and Coder work in parallel.
- **Parallel:** Designer can create `app/styles.css` while Coder creates `app/index.html` and `app/project-data.json` after that agreement; these file scopes do not overlap.
- **Sequential/integration dependency:** `.vscode/launch.json` must serve from `app/` and open the HTML entry point, so confirm those paths and the browser-side data loading before final launch validation.
- **Final review:** Run the app only after all parallel outputs are integrated; visual review and launch verification depend on the complete page.

## Validation and acceptance expectations

1. **Files and connections:** `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` exist. HTML uses the exact title `Project Pulse`, references the stylesheet and JSON data, and renders `.project-card` elements from the data.
2. **Data:** `app/project-data.json` is valid JSON with a top-level `projects` array. Every project has `name`, `owner`, `status`, `recentActivity`, `priority`, and a contributor-friendly summary. Check with `python3 -m json.tool app/project-data.json`.
3. **Visual and accessibility review:** CSS contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`; the layout adapts to narrow screens, uses readable contrast and spacing, exposes status/priority clearly, and provides keyboard-visible focus. Verify text is not clipped and all required project details are legible.
4. **Launch configuration:** `python3 -m json.tool .vscode/launch.json` succeeds. The configuration is named **Run Project Pulse Dashboard**, runs `python3 -m http.server 5500` with `cwd` `${workspaceFolder}/app`, and opens `http://localhost:%s/index.html` via `serverReadyAction`.
5. **End-to-end preview:** Start **Run Project Pulse Dashboard** in VS Code. Confirm the browser shows the Project Pulse dashboard (not a server directory listing), the JSON records render, and no missing-file/loading errors occur. Stop the server after checking.
6. **Exercise checks:** Ensure the output names and required phrases/structure align with the Step 2 and Step 3 checks in `.github/workflows/2-step.yml` and `.github/workflows/3-step.yml`. Those workflow checks verify files and key phrases; they do not replace the live preview and accessibility review.

## Risks and assumptions

- A browser may block `fetch()` from `file://`; the planned HTTP launch avoids this. Keep the document path at `/index.html` and the working directory at `app/` so the page, stylesheet, and JSON resolve together.
- Strict JSON has no comments; avoid trailing commas in both JSON deliverables.
- Status and priority labels should remain understandable without color alone. Ensure the example data is internally consistent and summaries stay concise.
- The launch uses port 5500; if it is occupied, stop the other server before retrying rather than changing the agreed preview URL without updating the configuration and checks.
