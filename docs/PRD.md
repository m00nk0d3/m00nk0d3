# PRD: Normandy - Engineering Factory Cockpit

## 1. Project Overview
**Normandy** is a high-performance, Go-based TUI "Cockpit" designed to act as the centralized Command & Control center for a multi-agent engineering factory. It allows a human architect to orchestrate complex development workflows, manage distributed agents (the "Pilot"), and oversee a repository's lifecycle through a unified, "Neural Link" inspired interface.

## 2. Target Audience
- Lead Engineers and Architects who need a "God View" of complex multi-agent projects.
- Developers who want to automate the "boilerplate" of workspace setup and issue management.

## 3. Technical Stack
- **Core:** Go (Golang)
- **UI:** TUI (Cyberpunk/Neural Link aesthetic: high contrast, neon accents, deep blacks)
- **Persistence:** SQLite (WAL mode) for a State Machine Registry.
- **Orchestration:** `tmux` for window/pane management.
- **Remote:** `gh` CLI for GitHub API interaction (Issues, PRs, Branching).
- **Communication:** Local HTTP Bridge for JSON telemetry from external Pi Agent processes.

## 4. Functional Requirements

### 4.1 The Neural Switch (Multi-Mode Cockpit)
The system must support four distinct views, switchable via hotkeys:
1.  **OVERVIEW (Pilot View):**
    *   Display a visual "Roadmap" (Node Map) of the current project.
    *   Display the "Active Thread": current Work Unit, high-level milestones, and a progress bar.
    *   Display a "Raw Data Stream": a live, scrollable feed of telemetry/logs from the active Pi Agent.
2.  **ISSUES (Project Manager View):**
    *   Fetch and display GitHub Issues.
    *   Support search/filter by label, state, and keyword.
    *   "Neural Conversion" action: Instantly convert a selected issue into a new Work Unit and workspace.
3.  **PRs (Gatekeeper View):**
    *   Fetch and display open Pull Requests.
    *   Display status tracking and quick-action links to review or merge via the `gh` CLI.
4.  **WORKTREES (Sandbox Manager):**
    *   Display a gallery of all active and historical worktrees.
    *   Show metadata: Branch name, associated Work Unit, and "Pilot" status (is an agent currently attached?).
    *   "Jump" button: Instantly teleport the user's focus to the associated `tmux` layout.

### 4.2 Workspace Provisioning
Upon activation of a Work Unit, Normandy must automatically provision a `tmux` workspace:
- **Layout:** 
    - Top-Left: Neovim (Development).
    - Top-Right: Pi Agent (Execution).
    - Bottom: Standard Terminal (Console).
- **Persistence:** Store the `tmux` window and pane IDs in the SQLite Registry.
- **Focus Logic:** Automatically switch the user's focus to the new workspace. Provide a hotkey (`Prefix + F`) to return to the Normandy Cockpit.

### 4.3 State Machine & Registry
- **SQLite Registry:** Must track:
    - Work Unit ID, Status (Queued, Active, Stuck, Success, Failed).
    - Retry counts for failed tasks.
    - Mapping of Work Units to specific `tmux` Window/Pane IDs.
    - Cache for Issues and PRs (refreshed via background heartbeat).

### 4.4 Communication & HITL (Human-in-the-Loop)
- **HTTP Bridge:** Listen for JSON payloads from external agents (e.g., `{"task_id": 14, "status": "STUCK", "error": "..."}`).
- **Coach Mode:** 
    - If a task is `STUCK`, trigger a visual alert in the OVERVIEW view.
    - Provide a "Hint Injection" UI to allow the user to provide a "Latest & Greatest" hint injection to the agent.
    - Use a "Latest & Greatest" logic where new hints overwrite old ones to prevent prompt bloat.

### 4.5 Design Specifications
- **Aesthetic:** Cyberpunk / Neural Link (High contrast, Neon Cyans, Acid Greens, Deep Blacks).
- **Navigation:** Tabbed hotkeys for mode switching.
- **Flow:** "Side-Quest" logic—stuck tasks move out of the main roadmap to prevent pipeline clogging.
