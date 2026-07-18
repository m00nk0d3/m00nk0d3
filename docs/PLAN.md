# Normandy: Hyper-Atomic Roadmap (PLAN.md)

This document contains the granular, atomic Work Units required to build the Normandy Cockpit. Each task is a single, isolated unit of work.

## Phase 0: Project Foundation
- [ ] Create project root directory.
- [ ] Initialize `go.mod` with `github.com/charmbracelet/bubbletea`, `lipgloss`, and `sqlx`.
- [ ] Create `/cmd/normandy` directory.
- [ ] Create `/internal/db`, `/internal/api`, `/internal/tmux`, `/internal/ui`, `/internal/github` directories.
- [ ] Create `/docs` and `/assets` directories.
- [ ] Create `.gitignore` (Go, SQLite, Tmux, OS).
- [ ] Create `README.md` with project description.
- [ ] Create `config.yaml` for initial settings.

## Phase 1: SQLite Persistence Layer
- [ ] Define `WorkUnit` struct in `internal/db`.
- [ ] Define `RegistryEntry` struct in `internal/db`.
- [ ] Define `CacheEntry` struct in `internal/db`.
- [ ] Create `db/sqlite.go` for connection handling.
- [ ] Implement `InitDB()` to create tables (Registry, WorkUnits, Cache).
- [ ] Implement `SaveWorkUnit()` function.
- [ ] Implement `UpdateWorkUnitStatus()` function.
- [ ] Implement `GetWorkUnitsByStatus()` function.
- [ ] Implement `GetCacheIssues()` function.
- [ ] Implement `GetCachePRs()` function.
- [ ] Implement `UpsertCacheIssue()` function.
- [ ] Implement `UpsertCachePR()` function.

## Phase 2: HTTP Bridge (Telemetry)
- [ ] Create `api/server.go` for HTTP setup.
- [ ] Define `TelemetryPayload` struct.
- [ ] Implement `POST /telemetry` handler.
- [ ] Implement `POST /hint` handler.
- [ ] Implement `POST /jump` handler.
- [ ] Implement `GET /registry` handler (for external agents).
- [ ] Implement `Bridge_Dispatcher` to route payloads to SQLite.
- [ ] Implement `Bridge_Logger` for debugging bridge traffic.

## Phase 3: GitHub Integration
- [ ] Implement `github/client.go` to wrap `gh` CLI.
- [ ] Implement `fetch_issues()` function.
- [ ] Implement `fetch_prs()` function.
- [ ] Implement `create_issue_from_cache()` function.
- [ ] Implement `sync_cache_loop()` background worker.
- [ ] Implement `get_repo_metadata()` function.

## Phase 4: Tmux Orchestrator
- [ ] Create `tmux/provider.go` interface.
- [ ] Implement `Tmux_Provider` struct.
- [ ] Implement `New_Window()` function.
- [ ] Implement `Split_Pane_Horizontal()` function.
- [ ] Implement `Split_Pane_Vertical()` function.
- [ ] Implement `Send_Keys()` helper.
- [ ] Implement `Focus_Window()` helper.
- [ ] Implement `Provision_Workspace()` (The "Layout" logic).
- [ ] Implement `Get_Active_Window_ID()` function.

## Phase 5: TUI Core & Styling
- [ ] Implement `ui/theme.go` with Cyberpunk color constants.
- [ ] Implement `ui/styles.go` for Lipgloss components.
- [ ] Setup `bubbletea` model and main loop in `cmd/normandy/main.go`.
- [ ] Implement `View_Switcher` logic.
- [ ] Implement `Global_Hotkeys` handler.
- [ ] Implement `Navigation_Handler` for Tab 1-4.
- [ ] Implement `Focus_Jump_Handler`.

## Phase 6: View: OVERVIEW (Pilot)
- [ ] Implement `Roadmap_Widget` (Node Map).
- [ ] Implement `Roadmap_Node` component.
- [ ] Implement `Active_Thread_Card` component.
- [ ] Implement `Progress_Bar` component.
- [ ] Implement `Milestone_List` component.
- [ ] Implement `Data_Stream_Widget` (Log scroll).
- [ ] Implement `Log_Buffer_Manager` for scrolling.
- [ ] Implement `Side_Quest_Logic` for visual movement.

## Phase 7: View: ISSUES (Project Manager)
- [ ] Implement `Issues_Table_Widget`.
- [ ] Implement `Issue_Row` component.
- [ ] Implement `Search_Bar_Widget`.
- [ ] Implement `Filter_Chip_Widget`.
- [ ] Implement `Neural_Conversion_Button`.
- [ ] Implement `Issue_Cache_Loader`.

## Phase 8: View: PRs (Gatekeeper)
- [ ] Implement `PR_List_Widget`.
- [ ] Implement `PR_Row` component.
- [ ] Implement `Status_Badge_Component`.
- [ ] Implement `Review_Action_Button`.
- [ ] Implement `Merge_Action_Button`.
- [ ] Implement `PR_Cache_Loader`.

## Phase 9: View: WORKTREES (Gallery)
- [ ] Implement `Worktree_Gallery_Widget`.
- [ ] Implement `Worktree_Card` component.
- [ ] Implement `Worktree_Metadata_Display`.
- [ ] Implement `Jump_To_Workspace_Action`.
- [ ] Implement `Lifecycle_Button_Group` (Suspend, Nuke).

## Phase 10: Workspace Provisioning Logic
- [ ] Implement `Workspace_Request_Handler`.
- [ ] Implement `Worktree_Creation_Logic` (Git command execution).
- [ ] Implement `Branch_Creation_Logic` (Git command execution).
- [ ] Implement `Provisioning_Sequencer` (Step 1-3 logic).
- [ ] Implement `Environment_Injector` (Set Env Vars for Tmux).

## Phase 11: Human-in-the-Loop (Coach Mode)
- [ ] Implement `Stuck_Detector` worker.
- [ ] Implement `Coach_Overlay_Widget`.
- [ ] Implement `Hint_Injection_API` (HTTP Bridge integration).
- [ ] Implement `Hint_Storage_Logic` (Latest & Greatest logic).
- [ ] Implement `Alert_Notification_System`.

## Phase 12: Integration & Polish
- [ ] Implement `Config_Loader` (YAML).
- [ ] Implement `Graceful_Shutdown` handler.
- [ ] Implement `Telemetry_Pulse_Animation`.
- [ ] Implement `Unit_Tests` for DB operations.
- [ ] Implement `Integration_Test` for Bridge $\rightarrow$ DB.
- [ ] Implement `End_to_End_Test` (Issue $\rightarrow$ WorkUnit $\rightarrow$ Tmux).
