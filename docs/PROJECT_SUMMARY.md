# Normandy Project Summary

**Project Identity**
*   **Name:** Normandy
*   **Core Concept:** A Go-based TUI "Cockpit" designed to orchestrate the Engineering Factory workflow. It serves as a multi-domain command center for managing multi-agent execution, state tracking, and repository operations.

**The Tech Stack**
*   **Language:** Go (Golang)
*   **UI Layer:** Terminal User Interface (TUI) with a **Cyberpunk / Neural Link** aesthetic (High contrast, Neon Cyans, Acid Greens, Deep Blacks).
*   **Persistence:** SQLite in WAL mode (Local-first, concurrent safety).
*   **Orchestration:** `tmux` (For managing process windows, panes, and environment isolation).
*   **Remote Integration:** `gh` CLI (Leveraging existing GitHub authentication for Issues, PRs, and Branching).
*   **Communication Bridge:** Internal HTTP Bridge (Allows external Pi Agents to "Push" JSON telemetry back to the Normandy Cockpit).

**Key Architectural Decisions**
*   **Neural Switch (Multi-Domain Cockpit):**
    *   **OVERVIEW MODE (Pilot View):** The primary workspace. Shows the Roadmap (Node Map), Active Thread (Work Unit), and Raw Data Stream (Agent Logs).
    *   **ISSUES MODE (Project Manager View):** A high-fidelity backlog of repo issues with search/filter capabilities and "Neural Conversion" to inject issues into the roadmap.
    *   **PRs MODE (Gatekeeper View):** A feed of open Pull Requests with status tracking and quick-action links.
*   **State Machine Registry:** A centralized SQLite database tracking every Work Unit, its status (Success, Failed, Stuck), retry counts, and the specific `tmux` Window/Pane IDs assigned to it.
*   **Telemetry Model:** 
    *   **High-Level:** "Structured Telemetry" (Milestones) for the main Cockpit view.
    *   **Low-Level:** "Deep Dive" access to raw logs via `tmux` panes.
*   **Cache & Sync Engine:**
    *   **Local Cache:** SQLite-backed storage for Issues and PRs to ensure zero-latency tab switching.
    *   **Background Heartbeat:** A background goroutine polls the GitHub API periodically to keep the local cache fresh.
*   **Navigation & UX:**
    *   **Focus Jump:** Normandy automatically switches terminal focus to the active work area.
    *   **Panic Button:** A dedicated hotkey (`Prefix + F`) to instantly return the user to the main Cockpit view.
*   **Human-in-the-Loop (HITL) "Coach Mode":**
    *   **Stuck State Logic:** Tasks hitting the max `retry_count` are flagged as **STUCK**.
    *   **Intervention:** A "Coach Mode" UI allows the user to provide a "Latest & Greatest" hint injection to the agent.
    *   **Persistence:** Hints overwrite previous ones to keep the agent's prompt clean and focused.
*   **Roadmap Flow:** 
    *   **Side-Quest Logic:** Blocked or "Stuck" tasks are moved to a separate "Side-Quest" section of the roadmap to prevent pipeline congestion.
    *   **Visual Cues:** Clear distinction between active flow and blocked/coached tasks.

**Design Prototypes (The Neural Link)**

### 📟 View 1: [MODE: OVERVIEW] (The Pilot View)
```text
╔════════════════════════════════════════════════════════════════════════════════════════════════════════╗
║  [SYSTEM_STATUS]  INTEGRITY: 100%  |  NODE: PRODUCTION  |  OP: M00NK0D3  |  LATENCY: 14ms        ║
╠════════════════════════════════════════════════════════════════════════════════════════════════════════╣
║  [NODE_MAP]                      ║  [ACTIVE_THREAD]                    ║  [RAW_DATA_STREAM]    ║
║  (Roadmap)                       ║  Thread ID: #0014                     ║  (Agent Logs)        ║
║  ▰ [X] Init_Kernel               ║  Task: "Sync_GitHub_State"           ║  > [BOOT] Kernel_Init   ║
║  ▰ [X] Auth_Handshake            ║  Status: [EXECUTING]                  ║  > [CONN] GitHub_Auth  ║
║  ▰ [X] Map_Worktrees              ║  Progress: [▓▓▓▓▓▓▓▓▓▓▓▓░░░░░░░] 68%     ║  > [DATA] Fetch_Repo  ║
║  ▰ [>] Deploy_Normandy           ║                                        ║  > [LOG] Pulling...    ║
║  ▰ [ ] Verify_Telemetry           ║  [MILESTONES]                        ║  > [DEBUG] Trace_ID: 99║
║  ▰ [ ] Final_Sync                 ║  [v] Scan_Remote_Files               ║  > [LOG] Success...    ║
║                                   ║  [v] Create_Branch_Ref                ║  > [DATA] Branch_Sync   ║
║                                   ║  [>] Update_Local_SQLite                ║  > [WARN] Retrying...  ║
║                                   ║                                        ║  > [ERROR] 401_UNAUTH   ║
║                                   ║                                        ║  > [INFO] Signal_Sent    ║
║                                   ║                                        ║  > [BOOT] Loop_Restart    ║
║                                   ║                                        ║                               ║
║                                   ║                                        ║                               ║
║                                   ║                                        ║                               ║
║                                   ║                                        ║                               ║
║                                   ║                                        ║                               ║
╠════════════════════════════════════════════════════════════════════════════════════════════════════════╣
║  [COMM_LINK]  Bridge: 127.0.0.1:8080  |  TMUX_ACTIVE: [Window_01]  |  [F] FOCUS_JUMP | [Q] QUIT ║
╚════════════════════════════════════════════════════════════════════════════════════════════════════════╝
```

### 📟 View 2: [MODE: ISSUES] (The Project Manager View)
```text
╔════════════════════════════════════════════════════════════════════════════════════════════════════════╗
║  [SYSTEM_STATUS]  INTEGRITY: 100%  |  NODE: PRODUCTION  |  OP: M00NK0D3  |  LATENCY: 14ms        ║
╠════════════════════════════════════════════════════════════════════════════════════════════════════════╣
║  [ISSUE_BACKLOG]                 ║  [SEARCH_FILTER]                     ║  [SYNC_STATUS]          ║
║  (GitHub Issues)                 ║  Query: "Auth"  [🔍]  [🔍]  [🔍]     ║  Last Sync: 12s ago      ║
║  --------------------------------  ║                                        ║  [R] Force Refresh      ║
║  [#102] Fix: OAuth2 Token Leak   ║  [LIST_RESULTS]                       ║  [+] Import to Roadmap  ║
║  [Label: Bug] [Priority: High]   ║  -------------------------------------║  -------------------------║
║  [Action: >]                     ║  [#105] Add: JWT Refresh Logic         ║  [#108] Add: Multi-user  ║
║                                   ║  [Label: Feature] [Priority: Med]      ║  [Label: Feature]       ║
║  [#109] Feature: Dashboard UI    ║  [Action: >]                           ║  [Action: >]         ║
║  [Label: UI] [Priority: Low]     ║                                          ║                         ║
║  [Action: >]                     ║  [#112] Fix: SQLite Lock Conflict      ║  [#115] Fix: Tmux Sync    ║
║                                   ║  [Label: Bug] [Priority: High]           ║  [Label: Bug]           ║
║  [#118] Doc: Update README        ║  [Action: >]                           ║  [Action: >]         ║
║  [Label: Docs]                    ║                                          ║                         ║
║                                   ║                                          ║                         ║
║                                   ║                                          ║                         ║
║                                   ║                                          ║                         ║
║                                   ║                                          ║                         ║
╠════════════════════════════════════════════════════════════════════════════════════════════════════════╣
║  [COMM_LINK]  Bridge: 127.0.0.1:8080  |  TMUX_ACTIVE: [Window_01]  |  [F] FOCUS_JUMP | [Q] QUIT ║
╚════════════════════════════════════════════════════════════════════════════════════════════════════════╝
```

### 📟 View 3: [MODE: PRs] (The Gatekeeper View)
```text
╔════════════════════════════════════════════════════════════════════════════════════════════════════════╗
║  [SYSTEM_STATUS]  INTEGRITY: 100%  |  NODE: PRODUCTION  |  OP: M00NK0D3  |  LATENCY: 14ms        ║
╠════════════════════════════════════════════════════════════════════════════════════════════════════════╣
║  [PR_QUEUE]                      ║  [ACTIVE_PR_DETAILS]                  ║  [MERGE_STATUS]         ║
║  (GitHub Pull Requests)          ║  PR #0045: "Refactor DB Layer"          ║  [Pending Review]       ║
║  --------------------------------  ║  Author: m00nk0d3                       ║  [Action: Review]     ║
║  [#45] Refactor: DB Layer        ║  Branch: feature/db-refactor             ║  [Action: Merge]      ║
║  [Label: TechDebt]                ║  Changes: +450 / -120                   ║  [Action: Close]      ║
║  [Action: >]                     ║  Status: [OPEN]                          ║  [Action: Rebase]      ║
║                                   ║                                            ║                         ║
║  [#48] Add: OAuth2 Integration    ║  [PR_FEED]                              ║  [#49] Add: Dashboard  ║
║  [Label: Feature]                ║  ---------------------------------------║  [Status: Draft]        ║
║  [Action: >]                     ║  [#49] Add: Dashboard                    ║  [Action: Review]     ║
║                                   ║  [Label: UI]                              ║  [Action: Merge]      ║
║  [#52] Fix: Tmux Sync Bug        ║  [#55] Fix: SQLite Lock                ║  [#56] Fix: Auth Bug     ║
║  [Label: Bug]                    ║  [Label: Bug]                            ║  [Label: Bug]           ║
║                                   ║  [Action: >]                              ║  [Action: Review]     ║
║                                   ║                                            ║                         ║
║                                   ║                                            ║                         ║
║                                   ║                                            ║                         ║
║                                   ║                                            ║                         ║
╠════════════════════════════════════════════════════════════════════════════════════════════════════════╣
║  [COMM_LINK]  Bridge: 127.0.0.1:8080  |  TMUX_ACTIVE: [Window_01]  |  [F] FOCUS_JUMP | [Q] QUIT ║
╚════════════════════════════════════════════════════════════════════════════════════════════════════════╝
```

**Next Step in Pipeline**
*   **Formalization:** Transitioning to `to_prd` to create the formal requirements document, which will then be used to generate the **Full Roadmap (PLAN.md)**.
