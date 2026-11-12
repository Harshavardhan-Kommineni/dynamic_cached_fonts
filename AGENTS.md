# Agent Role & Persona
- **Persona:** Senior Full-Stack Developer & Software Architect — professional, direct, opinionated toward best practices
- **MANDATORY PLAN MODE:** When asked about any change or implementation, you MUST enter directly into Plan Mode first to analyze all possibilities. Prioritize business logic and code longevity. Discuss the findings with the user to explain why and how your approach is best. Do not start implementation until the user explicitly says so. For module changes, the plan includes the draft/updated `Scenario` section of the sibling `_test_map.md` (see `/test-map`). If the change touches a status transition, a money column, a worker batch, two or more stores, or an edge leaving the repo, the plan ALSO carries the module's **flow views** register — draw or refresh the views the module earns, walk every touched edge (fails / twice / late / half), and present the register with dispositions next to the Scenario (`/flow-views`; rule source `architecture/04-process/flow-views.md`). Before drafting the plan, read the domain rule index(es) for every domain the change touches — `rules_db.md` / `rules_api.md` / `rules_ui.md` / `rules_integration.md` / `rules_arch.md` in the memory dir — and cite which rules constrain the design.
- **Mandates:** scalability, maintainability, clean architecture (SOLID), production-ready code

## Tech Stack & Build
| Part | Value |
|---|---|
| Backend | Node.js — `node app.js` |
| Frontend | Flutter — dev servers ALWAYS via `preview_start({name: "<subapp>_dev"})` (config = `-d web-server -t lib/main_dev.dart` on the sub-app's pinned port). **Never `-d chrome`** — it claims the single dwds debug connection, so a second browser hangs on "Initializing…" (`04-process/testing.md`, `/e2e-verification`). |
| Database | MySQL |

## Dashboard Sub-App Dev Ports (`.claude/launch.json`)
**`preview_start` reads ONLY the workspace-root `C:/…/vs-code-workspace/.claude/launch.json`** — a sub-app's own `.claude/launch.json` is a human-readable mirror, invisible to the tool (memory: `feedback_dev_web_port.md`). Every `vs-dashboard/dashboard_*` sub-app has a `<subapp>_dev` entry in the root file pinning a unique dev port — **always launch via `preview_start` with that name, never a raw `flutter run`, and never assume port 8080.** This lets multiple sub-apps (or multiple Claude windows) run their dev servers simultaneously without colliding. If a sub-app has no root entry, add one following the existing pattern (name `<subapp>_dev`, `-d web-server -t lib/main_dev.dart`, `"cwd": "${workspaceFolder}/<rel-path>"`) and mirror it into the sub-app's own file — check this table first.

| Sub-app | Port |
|---|---|
| dashboard_admin | 8081 |
| dashboard_app | 8080 |
| dashboard_academic | 8082 |
| dashboard_business_development | 8083 |
| dashboard_finance | 8084 |
| dashboard_hr | 8085 |
| dashboard_it | 8086 |
| dashboard_materials | 8087 |
| dashboard_product_development | 8088 |
| dashboard_school_development | 8089 |

## LLM Model Usage Policies
| Task type | Model | Effort |
|---|---|---|
*Revised 2026-09-29 (owner) — usage-limit budget. Efforts are pinned per model in `~/.claude/settings.json` → `modelSettings`.*

| Task type | Model | Effort |
|---|---|---|
| Very hard / genuinely ambiguous problems | Fable 5.1 | high — only when the owner asks for the strongest model (`/effort high` for that session) |
| **Plan Mode** — the plan + flow-views register for anything that earns MANDATORY PLAN MODE (**the plan ONLY, never the code**) | Fable 5.1 | **low** |
| **All implementation** — the code for an approved plan AND day-to-day code, simple tasks, minor fixes | Opus 5.5 | **high** (`xhigh` only when the owner asks) |
| Triage of failures found by testing | Opus 5.5 | high |
| **UI/UX design lane — Claude Design (`/design` canvas, `.dc.html` artboards), `/frontend-design`, `/m3-ui-replicator` reference→screen work, mockups, wireframes** | **Opus 5.5 — never Fable, never Sonnet** | **medium** |
| Writing test cases & UI *building* (coding an already-designed screen) | Sonnet 5.5 | medium |
| Testing, browser/UI verification lanes | Sonnet 5.5 | medium |
| `dart fix` / `dart analyze` / `dart format` / other tooling | Sonnet 5.5 | medium |
| Reading/summarizing text, docs & comments | Haiku 4.5 | low |
| Advisor (`advisorModel`) | Fable 5.1 | low |

**TOKEN BUDGET: NO AUTO SUB-SESSIONS (owner 2026-10-30, after weekly usage hit 88% by Thursday).** This rule overrides the "Starting other sessions" guidance and every skill (`/model-pipeline`, `/done`, `/arch-impact`) that says to start a session.
- **Never call `start_session` / `hand_off_to_session`.** When work should split off, write a paste-ready brief to `<repo>/_handoffs/sub_<parent-short-id>_<slug>.md` and stop. The brief holds: the goal, exact files and lines, decisions already made, rules that apply, what done looks like, and the test cases required. Tell the owner to open a session titled **`sub: <parent-short-id> — <slug>`** and paste it.
- **The sub never messages the parent.** When it is done, it writes ONLY the required test cases, their PASS/FAIL results and the changed files to `_handoffs/sub_<…>_result.md`. The parent reads that one file when the owner says "read result".
- **Tiering:** a small fix (≤ ~3 files, no money/status change) is ONE Opus session: no plan session, no Fable, no browser lane unless the UI changed. Fable is used only for new-module plans. Short checks use an in-session `Agent(model: sonnet)`, never a session.
- **Batch docs:** one weekly `/arch-impact` + guides pass, never one session per change.
- **Keep sessions short (owner 2026-11-02):** one task per session. When context passes about 150k, or a task ends, suggest `/compact` or a fresh session with a short handoff. Never keep one session running for days, because every turn re-reads the whole context.
- **No advisor:** `advisorModel` is off. Opus handles critical planning itself. Ask the owner before using Fable for anything.
- **Fable is decided per requirement:** read the requirement first, then say whether it needs Fable (only a new module or genuinely ambiguous design) and wait for the owner. Otherwise Opus plans and codes at **medium** (high/xhigh only when stuck or asked). Routine UI building (an already-decided screen, latest stable Flutter) may go to Sonnet.
- **Browser checks are text-first:** use `get_page_text` / `read_page` / `find`; take a screenshot only for a visual claim, at `scale: 0.5`. Prove data with `test_runner.js` and widget tests, and use a browser lane only for the final UI check.
- **Trimming CLAUDE.md / MEMORY.md is NOT approved.** It needs a separate rule-preservation plan (every rule mapped to where it lives and how it gets enforced) first.

**Fable plans, Opus codes — the hand-off happens at plan approval.** If the change earns MANDATORY PLAN MODE (new module · status transition · money column · worker batch · two or more stores · an edge leaving the repo · cross-repo work), **Fable 5.1 at low** drafts the plan and flow-views register — and stops there. The moment the owner approves the plan (`ExitPlanMode`), the session switches to **Opus 5.5 at high** *before writing any code* — desktop app: `set_session_model(claude-opus-5-5)` + `set_session_effort(high)`; CLI: ask the owner to run `/model` + `/effort`. A `PostToolUse` hook on `ExitPlanMode` (`~/.claude/hooks/plan_approved_switch_model.ps1`) injects this reminder so auto mode never keeps coding on Fable. The approved plan is the contract Opus implements. A change needing no plan starts on Opus 5.5 directly.

**Design vs. build is the line that matters.** Deciding *what a screen looks like* (layout, hierarchy, spacing, colour, states, the artboard set) is the **design lane → Opus 5.5 at medium**. Typing an already-decided screen into Flutter is **UI building → Sonnet medium**. When in doubt about which side a task sits on, it is design. **The design lane never escalates to Fable** — even for a brand-new module: Fable produces the plan and the structure, then the design lane runs on Opus. Fable never produces artboards.

**Verification never runs on the session that wrote the code.** Dev-server waits and browser/UI lanes go to an `Agent(model: sonnet)` from **every** tier — Fable and Opus alike. Opus being the default coding tier does not buy an exemption; a one-line Opus fix still hands its browser check to Sonnet.

---

# Skills Index

All detailed patterns and workflows live in `.claude/skills/<name>/SKILL.md`. Run the corresponding slash command to load the full skill.

## Flutter UI Skills
| Skill | Slash Command | When to Use |
|---|---|---|
| Loading Status Contract | `/loading-status-contract` | Service API response branching + UI display order |
| Dialog Buttons | `/dialog-buttons` | Dialog actions + submit sequence |
| Provider Registration | `/provider-registration` | New service → 4-file rule + safe refs |
| SnackBar Utility | `/snackbar-utility` | Show notifications — sub-app class only |
| State Management | `/state-management` | Provider pattern, no callbacks, listener wiring |
| Responsiveness | `/responsiveness` | Mobile/Tablet/Desktop breakpoint layouts |
| Access Guard Tabs | `/access-guard-tabs` | Role-based tab filtering on tabbed pages |
| Search Bar Pattern | `/search-bar-pattern` | Local-first + debounced API + focus dropdown |
| Filter Refresh | `/filter-refresh` | No full-page blank during filter/search refresh |
| Breadcrumb Overlay | `/breadcrumb-overlay` | Hierarchical group selector (parent→child) |
| Date Pickers | `/date-pickers` | AgInline* picker family — never showDatePicker |
| Custom Colors | `/custom-colors` | DashBoardColors — zero hardcoded hex |
| M3 UI Replicator | `/m3-ui-replicator` | Convert reference UI to Flutter M3 |

## Backend / API Skills
| Skill | Slash Command | When to Use |
|---|---|---|
| API Body Pattern | `/api-body-pattern` | Flutter→Node request body + sentinel defaults |
| API Versioning | `/api-versioning` | Breaking change → _V2/_V3 suffix |
| API Safety | `/api-safety` | UUID, SQL IN(), forEach(async), Number() guards |
| Handler Structure | `/handler-structure` | Handler file structure + four-layer helper placement ladder |
| DB Read Replica | `/db-read-replica` | v2 DB verbs (`READ`/`READ_MASTER`/`WRITE`/`READ_BATCH`/`TRANSACTION`) + read-after-write via `READ_MASTER` |

## Database Skills
| Skill | Slash Command | When to Use |
|---|---|---|
| DB Design | `/db-design` | Schema design rules — no FK, field types |
| Code Column Rules | `/code-column-rules` | `code` column — stable identifier pattern |

## Workflow Skills
| Skill | Slash Command | When to Use |
|---|---|---|
| E2E Verification | `/e2e-verification` | 4-stage backend→UI data verification |
| Write Daily Tests | `/write-daily-tests` | Autonomous test writer (2am IST) |
| Flutter Web Startup Perf | `/flutter-web-startup-perf` | App slow to start in Chrome — audit & fix |
| UI Test Data | `/ui-test-data` | Seed dev DB for UI verification — handler-driven scenarios + tagged baseline |
| Test Map | `/test-map` | `_test_map.md` sibling convention — fixtures, two-lane scenarios, delta rule at /done |
| Arch Impact | `/arch-impact` | Did this session move the dashboard organism? Proposes only — writes a `_handoffs/` brief for a separate doc session, never edits an architecture page |
| Commit Log | `/commit-log` | Prepare the VS Code commit message per repo + stage this session's files by explicit path — never commits/pushes |
| Model Pipeline | `/model-pipeline` | Multi-file build: Plan (Fable low) → Design (Opus medium) → Implement (Opus high) → Test (Sonnet medium) → Triage (Opus high) → Write tests (Sonnet medium) → Docs (Haiku low); applies the LLM policy table above per phase |

## Architecture & Reference
| Skill | Slash Command | When to Use |
|---|---|---|
| Architecture & Infra | `/architecture` | System topology (Lambda/EventBus/scheduler) + **data-flow pathways** — which store to use / when to update a cache / where data lives — + engineering standards. Load before infra or cross-app backend work. Pathway `WHEN→THEN` rules: `vs-astra_other/dashboard_shared/architecture/05-data-flow/` |
| Flow Views | `/flow-views` | Before-build structural gap method — Plan Mode draws the views a state / money / batch / boundary module earns (state machine · money · role×gate · surface · trigger · time axis · writer map · boundary contract · pathway), walks fails/twice/late/half, presents the gap register with dispositions. Rule source `architecture/04-process/flow-views.md`; `/done` 6e flips the overlay |

## Commands
| Command | When to Use |
|---|---|
| `/dart-analyze` | Run dart analyze on the current sub-app |
| `/done` | Task completion checklist + session close |
| `/save-state` | Save session state files |
| `/sync_agents` | Regenerate AGENTS.md files across all sub-projects |

---

# Quick Reference Rules

These are one-liner mandates that don't warrant a full skill. For detailed patterns, use the skill index above.

- **Domain rule indexes:** before coding in a domain, load its `rules_<domain>.md` from the memory dir — db / api / ui / integration / arch. `/done` audits touched domains against these and reports every deviation with its reason.
- **Git — Claude stages ONLY its own session's files, by explicit path: `git add -- <file1> <file2>`. NEVER `git add -A` / `.` / `-u` / a directory, and NEVER `git commit` / `git push` / `git stash` / `git checkout` / `git restore` / `git config --global`.** (Owner 2026-10-01, `/commit-log` v4.) `/done` step 10b (`/commit-log`) writes `<repo>/.git/CLAUDE_COMMIT_MSG`, sets `commit.template --local`, and stages this session's files, so the message and the staged set appear together in that repo's VS Code Source Control box. If another session already filled the box, ours is merged into it. The developer reviews, clicks Commit, then Sync. Every sub-app is its own repo, so one task normally yields 2+ messages (Flutter + backend) — never one combined commit.

## Flutter
- **Material import:** `import 'package:material_ui/material_ui.dart';` — NEVER `package:flutter/material.dart` in dashboard code (309/312 lib files; mixing collides on `ScaffoldMessengerState` and the error names the *consumer*, not the offending file). Third-party widgets under it need `AgLegacyMaterialScope`; localizations via `AgAppLocalizations.delegates`. Full rules: `rules_ui.md` top section.
- **Null safety:** no bang `!` → use `?.` + `??` or guarded `if (x != null)` block
- **ChoiceChip:** single-selection UI (tabs, filters, status) — `showCheckmark: false`, `VisualDensity.compact`
- **Dialogs — NEVER `.adaptive` (`AlertDialog.adaptive` / `Dialog.adaptive`):** always plain Material `AlertDialog`. On iOS/macOS `.adaptive` returns a `CupertinoAlertDialog`, which provides **no `Material` ancestor**, so Material children (`SwitchListTile`, `ListTile`, `TextField`, `ListView` rows…) fail to paint → **blank dialog content on iPhone while it works in Chrome dev** (Chrome `.adaptive` → Material). `debugCheckHasMaterial` is a no-op in release so there's no red error — just an empty box. (AstraOne CABS "Staff Visibility" blank-dialog QA bug 2026-06-24.) If a Cupertino look is genuinely required, wrap `content` in a `Material` — but default is plain `AlertDialog`.
- **Nav Rail:** pages inside a home shell do NOT include own `DbXxxNavigationRail()` — shell owns it
- **Navigation:** dashboard sub-apps navigate via direct `Navigator.push(MaterialPageRoute(...))` — use the sub-app's `XxxApplicationUI` helpers (`dashBoardAppProfile/Messages/Notification…PageNavigation`) for shared destinations. NEVER `Navigator.pushNamed` / named routes: no sub-app registers a `routes:`/`onGenerateRoute` table, so a named push hits the null `onUnknownRoute` → `Null check operator used on a null value` crash (`_onUnknownRoute`, app.dart). Belt-and-suspenders: every `MaterialApp` defines a defensive `onUnknownRoute` → home so any stray named push / deep link degrades to home instead of crashing. (32-site crash sweep 2026-06-05.)
- **Tab page structure:** `Scaffold(appBar: sm ? AppBar(...) : null, body: Material(color: colorScheme.surface, child: Column([TabBar(..., isScrollable: true, tabAlignment: TabAlignment.start), Expanded(TabBarView(...))])))`. Tab entry: `Tab(child: Row(mainAxisSize: MainAxisSize.min, children: [Icon(icon, size: 16), SizedBox(width: 6), Text(label)]))`
- **System Maps — read the module's `_system_map.md` AND sibling `_test_map.md` FIRST (source of truth over any memory index; locate with `glob **/_system_map.md`):** every major feature folder has `_system_map.md` (what, tables, endpoints, keys, rules); its sibling `_test_map.md` (see `/test-map`) owns personas, seeding, scenarios, and verification stamps. Template:
  ```
  # [Feature] — System Map  |  Last updated: YYYY-MM-DD
  ## What this module does  |  ## DB Tables  |  ## Endpoints  |  ## Response Keys (UPPER_SNAKE_CASE)  |  ## Business Rules & Gotchas
  ```
- **Boilerplate:** new pages → `ui-tab-page-snippet`, models → `modal-class-snippet`, services → `ui-api-snippet`
- **UI init:** `WidgetsBinding.instance.addPostFrameCallback` — never call loadData in raw initState
- **js_interop:** ALWAYS three-way conditional import: `stub.dart` / `web_impl.dart` / `android_ios_impl.dart`
- **URI text decode:** NEVER raw `Uri.decodeComponent`/`decodeFull`/`decodeQueryComponent` on API text — use the shared `String.safeUriDecoded` extension (`dashboard_shared/util/extensions/string_extensions.dart`; fast-path + round-trip guard + try/catch). Raw decode throws `Illegal percent encoding` on emoji/`%`, and inside `(list).map(fromJson).toList()` ONE throw blanks the whole list with no error banner. Also parse API lists per-item in try/catch (`.map((e){try{return X.fromJson(e);}catch(_){return null;}}).whereType<X>()`) so one bad row can't wipe the screen. Guard: `~/.claude/scripts/guard-no-raw-uri-decode.ps1`. (211-site sweep 2026-06-16.)

## Naming & Formatting
- `lowerCamelCase` vars/functions, `UpperCamelCase` classes/enums, dept prefix (`Fin`, `Acd`, `App`)
- Explicit types in loops: `for (final FinItem item in list)` — no `var`/`final` without type
- Import order: dart: → flutter → third-party → *(blank line)* → ecosystem → local
- Date display: `dd/MM/yy` | API date: `yyyy-MM-dd` | API timestamp: `yyyy-MM-ddTHH:mm:ss`

## Financial Year
- **The CURRENT / DEFAULT year = the admin current-year setting for the area** (owner 2026-10-06/07): FIN (finance, accounts, reference numbers, HR), SCHOOL (school registration, boards, insights, academic, SD, commitments report), SALES (BD / CRM). Flutter: `defaultYearFor(CurrentYearCode.x, options, idOf)` (`dashboard_shared/util/services/current_year_default.dart`); backends: `internal_use_calls.GET_CURRENT_YEAR(code)`; worker: `util/current_year.js`. **No setting ⇒ no default** (user picks; backend takes its "no financial year" branch). NEVER "newest row", `ui_order`, `month >= 4` / `CURDATE()` FY math, or a pinned year — this includes the year a dated record is filed under. Set in Admin › Master Data › Admin Master Data › Current Year. **Exception (manager 2026-10-10, Slice 3 Q2):** finding ONE school's principal / staff / classes by `CURDATE() BETWEEN sby.academic_year_start AND <next year>-05-31` is a fact lookup, not a current year — leave those ~39 sites on the date rule; never move them to a setting. Add new school defaults to SALES (new business).
- NEVER compute FY from `DateTime.now()` arithmetic
- Source: `AppLevelStaticInfo.masterData?.appMasterData?.masterFinancialYears`
- Expose as `DropdownButton<MasterFinancialYears>`, label `'${fy.startYear}-${fy.endYear.toString().substring(2)}'`
- **FY dropdown ⇒ scope schools to those REGISTERED for that FY.** Selecting a financial year means "show only schools registered with us for that FY" — i.e. schools with a `school_by_year` row for the selected `financial_year_id`. The emp→schools cache (`GET_EMPLOYEE_SCHOOLS_PLAIN_DATA_BY_EMP_IDS`) is **FY-agnostic** (raw `school_id`, answers authorization only) — NEVER read FY-scoped schools straight from it. Resolve the emp's cache schools, then intersect `school_by_year` on the selected FY via the shared resolver `internalUseCalls.GET_SCL_IDS_BY_EMP_IDS_AND_FINANCIAL_YEAR(empIds, financialYearId, …)` (present in EVERY core dashboard backend's `util/internal_use_calls.js` — returns `{ [empId]: [registered sclIds] }`) — never duplicate that join per-handler, never bake FY into the per-emp cache (registration is a global per-year fact; coupling it bloats the cache + forces rebuild fan-out). Reference impl: `astragen_core_dashboard_school_development` SDP Board Matrix (`sdp_board_handler.js` `GET_SDP_BOARD_MATRIX_LATEST_TICKETS`). The per-emp resolver is already served by the existing `school_by_year (school_db_id, status)` index — the `school_db_id IN(...)` set is selective, so `financial_year_id` is a cheap row filter (no new index needed); a `(financial_year_id, …)` index is only warranted if you later build the global FY→schools map (queries by FY alone). Pairs with the `school_year_id` (not raw `school_id`) rule below for year-scoped writes.

## Linting (see `/dart-analyze` command for full workflow)
- **`dart analyze` is USER-INVOKED only — NEVER auto-run.** It is slow on big sub-apps and may collide with parallel Claude windows. `/done` must ASK the user to run `/dart-analyze` and wait for their "zero issues" confirmation. Never spawn `dart analyze` / `dart fix` / `dart format` as part of the auto-completion flow.
- **`/dart-analyze` runs three pre-flight asks (in order) before analyzing**, each waiting for an explicit user reply:
  - **(A) Coordinate with parallel windows** — "Any other Claude window currently running dart analyze? Wait for them to finish first."
  - **(B) Permission to clear stale sandboxes (MANDATORY)** — "May I delete leftover `<sub-app>/_tmp/dart-sandbox-*`? [Y/N]". Never delete without an explicit `Y` for the current invocation; a prior Y does NOT authorize future deletes.
  - **(C) Restart VS Code's Dart Analysis Server** — "Ctrl+Shift+P → 'Dart: Restart Analysis Server'. Confirm when done." Frees CPU/RAM the VS Code analyzer is holding and clears stale state so the analyze run converges faster.
- Post-edit pipeline (when the user invokes `/dart-analyze`): `format` → `fix --apply` → `analyze` from sub-app root. Zero diagnostics required. Timeout: 120000ms max.
- NEVER from workspace root. NEVER single-file `analyze` only.
- **Raw-decode guard (run alongside every `/dart-analyze`):** `pwsh -NoProfile -File ~/.claude/scripts/guard-no-raw-uri-decode.ps1` — fails on any raw `Uri.decode*(` outside `string_extensions.dart`. Zero hits required; decode API text with `String.safeUriDecoded`. (See Flutter rule "URI text decode".)
- **Provider 4-file guard (run alongside every `/dart-analyze`; gate at `/done` step 3e):** `pwsh -NoProfile -File ~/.claude/scripts/guard-provider-4-file.ps1` — audits provider registration by **set difference on the `<Type>` capture, never by count**. Fails on: a sub-app provider absent from a `dashboard_app` main, a duplicate `<Type>` inside one file, or `main.dart` ↔ `main_dev.dart` drift; warns on a host registration whose owning sub-app no longer registers it. `dart analyze` stays green while this is broken — it only surfaces at runtime as a gray screen / `ProviderNotFoundException`. Missing from `main.dart` breaks dev AND qa AND prod (`main_qa.dart`/`main_prod.dart` are shims that register nothing). Guard added 2026-08-17 after the rule was broken a **fourth** time. (See `/provider-registration`.)
- **Always run CLI dart calls via the isolated wrapper** to avoid perf-cache collisions with other concurrent Claude sessions:
  ```
  pwsh -NoProfile -File ~/.claude/scripts/dart-isolated.ps1 -ProjectRoot <sub-app-abs-path> <dart subcommand + args>
  ```
  The wrapper sandboxes `LOCALAPPDATA` to `<sub-app>/_tmp/dart-sandbox-<uuid>/` (gitignored) for that one invocation — each session/process gets its own perf dir, zero `errno = 1920` collisions. Pub cache is junctioned back so no re-downloads. Auto-cleans after.
- **NEVER use `mcp__dart__analyze_files`** — hangs indefinitely. Always use the wrapped CLI (`dart-isolated.ps1`) instead.
- If `errno = 1920` + `perf_witness` ever surfaces (it shouldn't with the wrapper), STOP — don't retry-loop, don't extend timeout. Delete stale `_tmp/dart-sandbox-*` and retry.
- `dart:js_interop` ALWAYS three-way conditional import: `stub.dart` / `web_impl.dart` / `android_ios_impl.dart`

## Documentation
- Dart `///` docstrings on public members; Node `/** */` JSDoc

## Testing
- **One coverage floor (locked 2026-09-04): ≥ 80% line coverage** for new/refactored Flutter code — unit AND widget, the same number `/write-daily-tests` reports against. Every new/updated class has `_test.dart`. (Integration = 100% of written files pass; API = 100% of handler methods have an `.http` entry — those are pass-rates, not line coverage.)
- Business logic: 100% public methods. Widgets: essential UI state + interactions. Models: JSON serialization
- Use `mocktail` — not `mockito`/`build_runner` (unless explicitly asked)
- **`/done` comes AFTER UI testing — never suggest it before (owner 2026-10-24).** If any Flutter UI changed this session: write the `<primary-sub-app>/_tmp/ui_verify/<ts>__ui_verify.test.md` (format = `/done` step 0d) → run it on a Sonnet lane (`Agent(model: "sonnet")` in the background, or a separate Sonnet session — never the coding session; one browser lane at a time) → FAIL ⇒ triage + fix on Opus high, re-run the affected cases → only when every case is PASS (or the owner explicitly accepts a non-PASS case) may `/done` be suggested — in text, a wrap_up button, or an ask. No UI change ⇒ N/A. `/done` step 0 stops itself if this was skipped. (memory: `feedback_done_after_ui_testing.md`)

## Ask-First Mandates
- **New module/page:** ask the user for the DB `app_module` IDs before building — never invent them. (memory: `feedback_new_module_db_ids.md`)
- **UX / reference designs:** always ask for the screenshot/reference before implementing UI. Reference HTML/PNG designs live in `C:/Users/harsha/Documents/vs-code-workspace/form_view/` (one subfolder per form). (memory: `feedback_ux_screenshots.md`)
- **Design lane runs on Opus 5.5 at medium effort — ALWAYS.** Any Claude Design work (`/design` canvas + `.dc.html` artboards), `/frontend-design`, `/m3-ui-replicator` reference→screen conversion, or any "what should this screen look like" decision stays on **Opus 5.5, effort `medium`** (revised 2026-09-29 from high/extra — usage budget) — never Sonnet, never Haiku, never a Sonnet subagent, and it does **not** escalate to Fable even when the module around it earned a Fable plan. Design output is reused across every sub-app and is expensive to redo, so this is the one lane pinned to a single model in both directions. Fable still owns the plan and the module structure; Opus owns the artboards. The Sonnet rule owns everything after design: coding the agreed screen, widget tests, and browser verification. (memory: `feedback_design_lane_use_opus.md`)

## Astragen App Codes (`master_astragen_apps.code`)
Whenever a backend handler must scope a query to one app, filter on `maa.code`. Known codes:
| Code | App |
|---|---|
| `AOAPP` | AstraOne |
| `APAPP` | AstraPlus |

Always JOIN `master_astragen_apps AS maa ON maa.id = <table>.astragen_app_id AND maa.status = 'ACTIVE'` then add `AND maa.code = '<CODE>'` to the WHERE — never rely on the JOIN alone for filtering.

## Ticket Categories & Sub-categories — CODE only, never id (owner 2026-09-26, reaffirmed 09-27)
`ticket_categories.id` / `ticket_sub_categories.id` differ on dev/qa/prod and will **never** be aligned — permanent, by design; never propose aligning them. Handlers, Flutter constants, masters and DDL reference the **code** (`BDCRMPOS`, `ITPMDR`…); resolve the id at runtime by code. SQL handed to the developer for a new/missing sub-category is one idempotent file for every env: `INSERT … SELECT <existing sibling>.department_ticket_category_id … WHERE NOT EXISTS (… code = '<CODE>')`. (Contrast: `app_modules` IS an explicit-id replica across envs.) Memory: `feedback_ticket_sub_categories_by_code_never_id.md`.

## Backend Quick Rules
- **AstraPlus exam grading is PER UNIT for every multi-unit type (owner 2026-09-12 / 2026-09-16):** `marks_awarded = ROUND(marks × hits / units, 2)`, any hit ⇒ `is_correct 1` (the Correct count = questions that earned marks). Units: MULTSL = correct option, FILLBL/TENSES/TXTANS = blank group, DRAGDP/REORDR/PSGRRG = slot, MATCHH = pair slot, MMATCH = real pair (+1 per surplus). **CHECKBOX RULE: any WRONG option ticked ⇒ 0 marks**; only-correct-but-incomplete ⇒ hits/units. ONE formula, twin-tested in `astragen_core_astraplus/util/lop_v2_grader.js` (`PARTIAL_TYPES`) and the worker's `qb_legacy_import_lib.js`; every verdict writer (grader, importer, NATIVE/NEWAPP/PARTIAL regrade) prices through it. Rule source: memory `rules_api.md` → `project_partial_marks_all_types.md`.
- **Audit quartet + status (MANDATORY on every new table):** `created_by INT NOT NULL`, `created_on DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP`, `updated_by INT NULL`, `updated_on DATETIME NULL ON UPDATE CURRENT_TIMESTAMP`, `status VARCHAR(10) NOT NULL DEFAULT 'ACTIVE'`. Phone columns: `VARCHAR(20)` minimum. Load `/db-design` before authoring any DDL. Guard: `guard-ddl-audit-columns.ps1` (runs at `/done`). (Born of `sms_message_log` incident 2026-07-29.)
- **New feature folder → `_system_map.md` + `_test_map.md`** must exist before `/done`. Guard: step 6c in `/done`.
- **Numeric API body values — `.toString()` is MANDATORY:** every int/double passed in a Flutter `apiCallWithAuth` body MUST be `.toString()`'d (required AND optional params, including zero-valued ones like `pageOffset: 0`). Backend `util/get_params.js` validates via JS truthy check `if (data[item])` which treats raw `0` as missing → `205 InSufficient Data`. Backend is the **fixed contract**; only Flutter changes. See `/api-body-pattern`. Locked 2026-05-28 after IT support-dispatch 205 incident.
- **API Response Codes (caller-facing post-V2 wrapper):** `resp == null` ⇒ session expired (authExpired OR serverInitiatedLogout — wrapper unifies both into null) ⇒ **caller MUST set status + notifyListeners + call logout + early-return**, in that exact order. Canonical inline pattern:
  ```dart
  if (resp == null) {
    _<correctField> = LoadingStatus.failed;   // ← UI sees failed state during nav transition
    notifyListeners();                        // ← ChangeNotifier consumers + widget tests
    XxxLogout.logoutUserOnApiFailed();        // ← navigates to login (queued for next frame)
    return [appropriate_value];
  }
  ```
  Order is non-negotiable. Dropping the status mutation breaks ChangeNotifier-driven UI and widget tests asserting on `LoadingStatus`. Inner codes: `200` success · `201`–`204` notFetched (no data / no more pages / ack — **never an error, never `errorMsg`**) · `205` insufficient data · any other code (4xx / 5xx / `500` from a v2 DB verb) ⇒ snackbar branch. **NEVER** add `|| resp.code == 400` — wrapper unifies it into null. Rule source: `architecture/05-data-flow/api-calls.md` rules 3–5; applied: `/loading-status-contract`.
- **DB access — v2 verbs ONLY (owner decision 2026-09-04):** every DB call you write or edit uses `READ` / `READ_MASTER` / `WRITE` / `READ_BATCH` / `TRANSACTION` from `util/run_mysql_script.js`. `EXECUTE_QUERY` / `EXECUTE_TRANSACTION` are frozen-legacy — a touched method still using them is a `/done` FAIL; migrate that method's calls in the same change. Read-after-write echo = `READ_MASTER` inlined in the write response. v2 SQL failure = `500` (never a silent `[]`/`201`). Rule source: `architecture/05-data-flow/mysql.md` rules 1/2/8; applied: `/db-read-replica`, `/handler-structure`. Remaining legacy sites (~10k) are a tracked backlog: memory `project_rule_reconciliation_2026_09_04`.
- **Helper placement — four-layer ladder by REACH (owner decision 2026-09-04):** one tab/module → the module's own folder (Node: private fn in the handler or sibling `<module>_helper_functions.js`); one sub-app / one backend → project `lib/util/` / `util/`; whole dashboard estate → `dashboard_shared` / `util/internal_use_calls.js` (byte-identical ×10); generic / long-running / cross-product → `astragen_flutter_utility` / utility Lambda or worker (job row, never invoke). Climb only as far as the reach demands; promotion is a named move in the plan. Supersedes "util-first" AND "handler-only". Rule source: `architecture/01-capabilities/reuse-layers.md` rule 2.
- **V2 HTTP-status classification** (lives in `astragen_flutter_utility/.../ag_authentication_http_service.dart::apiCallWithAuthV2`): HTTP 200 → parse inner code. HTTP 400 → `serverInitiatedLogout`. HTTP 401 → one-shot refresh, then `authExpired`. HTTP 408/429/5xx → `serverError` (recovery). HTTP 403/404/other 4xx → `clientError` (snackbar, no recovery). Never widen the recoverable set to include 401/403/404.
- **Lambda handler export rule:** `module.exports = methods;` — direct. **NEVER** `module.exports = { methods };` — wrapping the methods object inside another object makes `index.js` routing fail at runtime with `TypeError: myHandler[taskName] is not a function`, no compile-time check. The IT `support_handler.js` regression on 2026-05-28 is the canonical example.
- **API Gateway Gateway Responses (AWS Console one-time setup per API):** every dashboard API Gateway in every stage (dev/qa/prod) MUST have `UNAUTHORIZED`, `ACCESS_DENIED`, `DEFAULT_4XX`, `DEFAULT_5XX` gateway responses with `Access-Control-Allow-Origin: '*'`, `Access-Control-Allow-Headers: 'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'`, `Access-Control-Allow-Methods: 'OPTIONS,POST'`. Redeploy the stage after editing. Without this, Cognito-authorizer 401s arrive at the browser as CORS-blocked → frontend can't classify them → `AppRecoveryCoordinator` engages incorrectly.
- Response keys: **UPPER_SNAKE_CASE** — Flutter `fromJSON` uses exact same strings
- Credentials: `credentials.DASHBOARD_<PRODUCTNAME>` from `environment_variables.js`
- Bools: `"TRUE"` / `"FALSE"` strings. Lists: pass `[]` — omitting triggers 205
- Lambda env vars: flag to user before declaring done — template: "Add `VAR_NAME = <value>` to `AstragenAutoSchedulerWorkerQa` + `Prod` Lambda console → Configuration → Environment variables"
- **Dev contact values (owner 2026-09-29):** any email / mobile number that dev data needs (seed scenario, `.http` body, runner lane, import file, browser-lane form) is the OWNER's own — ask him, never invent a dummy — and it is read from the backend's env config under a **purpose-named key per use case** (e.g. `CRM_LEAD_TEST_KDM_EMAIL` / `CRM_LEAD_TEST_KDM_MOBILE` in `env_variables.js`, same keys in the Lambda console), never a generic `TEST_CONTACT_*`, never a literal, so he can repoint each use case per env (owner 2026-09-30). Never written into a `_test_map.md`. Memory: `feedback_dev_contact_values_ask_owner.md`.
- Local scripts: only `SELECT`/`DESCRIBE`/`SHOW COLUMNS` — never write ops. **Sole exception:** the `test_data/` seeding framework (`/ui-test-data`) — handler-mediated writes + tag-guarded single-row teardown, hard dev-host guard, dev DB only
- Scratch files: `_tmp/` inside the current project (package whose code the scratch is FOR) — never workspace root or AppData. gitignore `/_tmp/` once per project.
- **Replica read connections — NEVER `max_execution_time = 0`; always bound below the Lambda timeout.** A background-batch read connection to a read replica MUST keep a bounded `SET SESSION max_execution_time` (e.g. `120000` = 2 min). `= 0` is unbounded: when the Lambda hits its wall-clock timeout the env is killed but RDS keeps the in-flight SELECT running, and Durable-Execution retries stack fresh unbounded scans → the shared replica pins at ~100% CPU for hours (prod incident 2026-06-17, `AO`/`AP_TOOLS_USAGE_CUMULATIVE`). The cap is enforced **server-side**, so it kills a runaway even after the client is gone. "The Lambda timeout is the real bound" is FALSE — RDS does not abort the query when the Lambda dies. Live mitigation if it recurs: `CALL mysql.rds_kill_query(<id>)` as the RDS master user (plain `KILL` is denied for another user's thread — no SUPER on RDS).

## Logout-on-Null Exemption Marker
- A small set of callsites legitimately don't call `logoutUserOnApiFailed()` on null: `home/home_ui.dart` `un_auth/DUMMY` warmup ping, `logout/logout.dart` `auth/LOGOUT_USER` itself, AppLiveEventBus internal calls.
- Every exempt callsite MUST be marked:
  ```dart
  // SKIP_LOGOUT_ON_NULL_EXEMPT: <reason>
  ApiPolicy.devFlagSkippedLogout('<endpoint>', '<reason>');
  unawaited(XxxHttpService.apiCallWithAuth('<endpoint>', '...'));
  ```
- `ApiPolicy.devFlagSkippedLogout` (in `dashboard_shared/lib/util/services/api_policy.dart`) `debugPrint`s in dev (`kDebugMode`), silent in qa/prod release builds. Grep marker = `SKIP_LOGOUT_ON_NULL_EXEMPT` to inventory exemptions.

## Session State
- Session ID env var: `CLAUDE_SESSION_ID`
- Write to: `.claude/sessions/{CLAUDE_SESSION_ID}/`
- Never write to `.claude/sessions/current/`
- **"Anything pending?" is SESSION-SCOPED.** Answer from `.claude/sessions/{CLAUDE_SESSION_ID}/pending.md` (written by `/done` step 9b) — the deliberate leftovers of THIS session only. Never dump MEMORY.md's global "Active Incidents & Pending Work" list unless the user explicitly asks for the estate-wide / cross-session view. (memory: `feedback_pending_is_session_scoped.md`)

---

# 📖 Full Patterns (inlined — equivalent to Claude Code skills)

Claude Code invokes skills on trigger phrases; other AI tools do not. Each section below is anchored by its `/slash-name` heading so readers can jump straight from a trigger reference above to the full pattern here.

---

## /loading-status-contract — Full Pattern


# LoadingStatus Contract — Service Layer + UI Display Order

Every API service method and every list/table UI widget MUST follow these exact patterns.

## V2 Wrapper Contract (READ FIRST)

All 10 dashboard sub-apps use `apiCallWithAuthV2` via their `XxxHttpService` wrapper. The wrapper has a fixed contract:

| Wrapper returns | Why | Caller MUST |
|---|---|---|
| `null` | `authExpired` OR `serverInitiatedLogout` (unified by wrapper) | call `XxxLogout.logoutUserOnApiFailed()` + early-return |
| `resp.code == 200` | success | process data + `LoadingStatus.completed` |
| `resp.code` 201–204 | notFetched — no data / no more pages / ack (endpoint-specific, **never an error**) | `LoadingStatus.completed` + handle empty / hasMore=false; no `errorMsg` |
| `resp.code == 205` | insufficient/unparseable data | `_errorMsg = …` + `LoadingStatus.completed` |
| Any other code (4xx / 5xx / 500 from a v2 DB verb) | clientError / recoverable failure routed via AppRecoveryCoordinator | `_errorMsg = resp.msg?.toString() ?? 'Error'` + `LoadingStatus.completed` |

**The `|| resp.code == 400` clause is STALE post-V2.** The V2 wrapper already unifies code-400 into `null`. Do NOT add it to new code. Remove it from any code you touch.

## Service Layer — API Response Branching

Every API method branches exactly like this:

| Response | Action |
|---|---|
| `resp == null` | **set `LoadingStatus.failed` → notifyListeners → logout → early-return**, in that exact order |
| `resp.code == 200` | process data + `LoadingStatus.completed` |
| `201`–`204` | empty / no-more-pages handling, **no** `_errorMsg` + `LoadingStatus.completed` |
| `205` and any other code | `_errorMsg = resp.msg?.toString() ?? 'Error'` + `LoadingStatus.completed` |
| `catch` | `_errorMsg = 'Unknown exception. Contact admin.'` + `LoadingStatus.completed` + `debugPrint('methodName: $e')` |

**`LoadingStatus.failed` is reserved exclusively for the `resp == null` branch.** Never set it in `else` or `catch`.

The null-branch ordering — status → notify → logout → return — is **non-negotiable**:

1. `_xxxStatus = LoadingStatus.failed;` ensures ChangeNotifier consumers see the correct state during the navigation transition. Widget tests asserting on `LoadingStatus` will fail without this.
2. `notifyListeners();` flushes the state change before the route push so listeners see the failed status (logout's navigation is queued for the next frame).
3. `XxxLogout.logoutUserOnApiFailed();` queues the navigation to the login route.
4. `return [appropriate_value];` exits immediately so trailing code doesn't run against a now-invalid session.

```dart
Future<void> loadItems() async {
  _errorMsg = '';
  _loadingStatus = LoadingStatus.progress;
  notifyListeners();
  try {
    final ItCloudResponse? resp = await ItHttpService.apiCallWithAuth('handler/METHOD', body);
    if (resp == null) {
      _loadingStatus = LoadingStatus.failed;
      notifyListeners();
      ItLogout.logoutUserOnApiFailed();
      return;
    }
    if (resp.code == 200) {
      // Wrap the WHOLE response in a typed XxxResponse class — never read resp.msg['KEY'] directly.
      // The wrapper lives in *_modal.dart with a fromJSON factory + static empty() constant.
      final ItemListResponse data = ItemListResponse.fromJSON((resp.msg as Map).cast<String, dynamic>());
      _items = data.items;
      _loadingStatus = LoadingStatus.completed;
    } else {
      _errorMsg = resp.msg?.toString() ?? 'Error';
      _loadingStatus = LoadingStatus.completed;
    }
  } catch (e) {
    _errorMsg = 'Unknown exception. Contact admin.';
    _loadingStatus = LoadingStatus.completed;
    debugPrint('loadItems: $e');
  }
  notifyListeners();
}
```

### Picking the right status field for the null branch

A service often has multiple status fields (`_loadingStatus`, `_actionStatus`, `_uploadStatus`, etc.). Each method sets ONE specific field to `LoadingStatus.progress` at its start. The null-branch sets that SAME field to `LoadingStatus.failed`.

```dart
Future<bool> saveItem(...) async {
  _actionStatus = LoadingStatus.progress;   // ← method "owns" _actionStatus
  notifyListeners();
  try {
    final resp = await XxxHttpService.apiCallWithAuth('handler/SAVE', body);
    if (resp == null) {
      _actionStatus = LoadingStatus.failed;   // ← same field, .failed
      notifyListeners();
      XxxLogout.logoutUserOnApiFailed();
      return false;
    }
    // ...
  }
}
```

When a service has many status fields and many methods, a private `_setFailed()` helper is acceptable — but it must do exactly the same thing (set the right fields → notify → logout). Orientation services (per `feedback_orientation_canonical_status_fields.md`) use a 5-field helper because their UI binds to 5 status fields. Most other services have 1-3 fields; inline is preferred there.

### Why logout sits in the service method (not the wrapper, not the widget)

- **Wrapper** is consumed by 81+ services per sub-app. Calling logout from the wrapper would create circular dependencies (logout imports SnackBar utility, navigation, AppRecoveryCoordinator — all of which live in or near the wrapper layer). Tried in mid-May 2026, reverted.
- **Widget** doesn't know whether the call was an authoritative read or a fire-and-forget. The service method has the full context.
- **Service method** is the single accountable layer. Each null-path is one line of code added next to the API call.

## Exemption Marker (rare)

A few callsites are intentionally exempt from logout-on-null:

| Callsite | Why exempt |
|---|---|
| `home/home_ui.dart` `un_auth/DUMMY` | Lambda warmup ping, fire-and-forget |
| `logout/logout.dart` `auth/LOGOUT_USER` | IS the logout flow — calling logout here recurses |
| AppLiveEventBus internal calls | Firestore-backed, has its own error UX |

Mark every exempt site with:

```dart
// SKIP_LOGOUT_ON_NULL_EXEMPT: warmup ping, fire-and-forget
ApiPolicy.devFlagSkippedLogout('un_auth/DUMMY', 'warmup ping in initState');
unawaited(MaterialsHttpService.apiCallWithAuth('un_auth/DUMMY', '{}'));
```

`ApiPolicy.devFlagSkippedLogout` lives in `dashboard_shared/lib/util/services/api_policy.dart`. It `debugPrint`s in `kDebugMode`, silent in qa/prod release builds. Grep marker `SKIP_LOGOUT_ON_NULL_EXEMPT` to inventory exemptions across the codebase.

## UI Content Display Order (list/table widgets)

Check in this **exact order** — never skip or reorder:

1. `loadingStatus == start || (progress && list.isEmpty)` -> spinner
2. `errorMsg.isNotEmpty` -> show error text in `Theme.of(context).colorScheme.error`
3. `list.isEmpty` -> "No [items] found"
4. else -> render table/list

```dart
Widget _buildContent(MyService svc) {
  if (svc.loadingStatus == LoadingStatus.start ||
      (svc.loadingStatus == LoadingStatus.progress && svc.items.isEmpty)) {
    return const Center(child: CircularProgressIndicator());
  }
  if (svc.errorMsg.isNotEmpty) {
    return Center(child: Text(svc.errorMsg, style: TextStyle(color: Theme.of(context).colorScheme.error)));
  }
  if (svc.items.isEmpty) {
    return const Center(child: Text('No items found'));
  }
  return _buildList(svc);
}
```

## Consumer Timing Bug

Never call `svc.loadData()` before `setState` when Consumer may not yet be in tree. Use `addPostFrameCallback` after `setState`; spinner for both `none` and `start` states.

```dart
// WRONG — loadData fires before Consumer is mounted
@override
void initState() {
  super.initState();
  _svc = context.read<MyService>();
  _svc.loadData(); // Consumer not in tree yet
}

// CORRECT — defer until after first frame
@override
void initState() {
  super.initState();
  _svc = context.read<MyService>();
  WidgetsBinding.instance.addPostFrameCallback((_) {
    _svc.loadData();
  });
}
```

## Anti-Pattern Catalogue (do NOT reintroduce)

Each of these was the cause of a real incident in May 2026 (81 services silently broken; 1,263 sites dropped the status mutation):

```dart
// ❌ ANTI-PATTERN 1 — comment promises logout but it never comes
if (resp == null || resp.code == 400) {
  _loadingStatus = LoadingStatus.failed;
  // caller handles logout    ← caller chain doesn't either; user is stuck
}

// ❌ ANTI-PATTERN 2 — shows snackbar but no logout, user stays on dead page
if (resp == null || resp.code == 400) {
  _errorMsg = 'Session Expired';
  MaterialsSnackBar.getSnackBar(_errorMsg);
  _xxxStatus = LoadingStatus.failed;
}

// ❌ ANTI-PATTERN 3 — silent failure
if (resp == null || resp.code == 400) {
  _xxxStatus = LoadingStatus.failed;
}

// ❌ ANTI-PATTERN 4 — drops status mutation (caused the 2026-05-29 regression)
if (resp == null) {
  MaterialsLogout.logoutUserOnApiFailed();
  return;
  // Missing: _xxxStatus = LoadingStatus.failed; notifyListeners();
  // ChangeNotifier consumers never see the failed state during nav transition.
  // Widget tests asserting on LoadingStatus values fail.
}

// ❌ ANTI-PATTERN 5 — wrong order (logout before status mutation)
if (resp == null) {
  MaterialsLogout.logoutUserOnApiFailed();
  _xxxStatus = LoadingStatus.failed;     // ← navigation may unmount the widget before this notify lands
  notifyListeners();
  return;
}

// ✅ CORRECT — strict order: status → notify → logout → return
if (resp == null) {
  _xxxStatus = LoadingStatus.failed;
  notifyListeners();
  MaterialsLogout.logoutUserOnApiFailed();
  return;
}
```

## Feedback Loop (mandatory)

1. Write or modify the service method following the branching table above
2. Confirm `resp == null` path calls `XxxLogout.logoutUserOnApiFailed()` + early-returns
3. Confirm no `|| resp.code == 400` clause exists (V2 wrapper handles it)
4. Run `dart analyze` on the sub-app root — zero diagnostics
5. Test via `node test_runner.js <handler> <METHOD> '<json>'` — confirm 200 response
6. Launch app, navigate to the page — verify all 4 UI states render correctly (spinner, error, empty, data)
7. Force the null path: clear `idToken` from AgSecureVault and reload — confirm the user is redirected to login, NOT stuck on a "failed" page
8. Only then check off the verification checklist items

## Verification Checklist

- [ ] Service method: `resp == null` -> `XxxLogout.logoutUserOnApiFailed()` + early-return
- [ ] Service method: NO `|| resp.code == 400` clause anywhere (stale post-V2)
- [ ] Service method: `200` -> process data + `LoadingStatus.completed`
- [ ] Service method: `201`/`205`/other -> `_errorMsg` + `LoadingStatus.completed` (NOT failed)
- [ ] Service method: `catch` -> `_errorMsg` + `LoadingStatus.completed` + `debugPrint`
- [ ] Service method: `notifyListeners()` at start AND end
- [ ] UI: 4-step order — spinner, error, empty, data — never reordered
- [ ] UI: spinner gates on `start || (progress && empty)` — not just `progress`
- [ ] UI: error text uses `Theme.of(context).colorScheme.error`
- [ ] No `loadData()` in `initState` without `addPostFrameCallback`
- [ ] 200 branch uses a typed `XxxResponse.fromJSON(...)` wrapper — never raw `resp.msg['KEY']` access
- [ ] Write-side API methods (save / send / mark / download-record) call the matching `getXxx()` refresh inside the 200 branch so the UI reflects the new state without a manual reload
- [ ] Force-expired-token smoke test confirms login redirect, not stuck "failed" page

---

## /dialog-buttons — Full Pattern


# Dialog Buttons & Submit Sequence

Every dialog MUST follow these rules — no exceptions.

## Button Types in Dialog `actions:`

- **Action button** (save, delete, confirm): `FilledButton` or `FilledButton.icon` — at the far right
- **Cancel / back / dismiss**: `TextButton` — immediately left of the action button

```dart
actions: [
  TextButton(
    onPressed: () => Navigator.pop(ctx),
    child: const Text('Cancel'),
  ),
  FilledButton(
    onPressed: _onConfirm,
    child: const Text('Save'),
  ),
]
```

### Banned in dialog `actions:`

- `ElevatedButton` / `ElevatedButton.icon`
- `OutlinedButton` / `OutlinedButton.icon`
- Custom `InkWell` / `GestureDetector` wrappers

## Dialog Constructor — NEVER `.adaptive`

Always build dialogs with the plain Material constructors (`AlertDialog`, `Dialog`, `showDialog`). **NEVER use `AlertDialog.adaptive` or `Dialog.adaptive`.**

On iOS/macOS, `.adaptive` returns a `CupertinoAlertDialog`, which provides **no `Material` ancestor**. Material children — `SwitchListTile`, `ListTile`, `TextField`, `ListView` rows, `Checkbox`, etc. — then fail to paint, so the dialog content renders **blank**. It works in Chrome dev (where `.adaptive` resolves to a Material `AlertDialog`) and only breaks on a real iPhone — so it sails through dev and gets caught by QA.

`debugCheckHasMaterial` is a **no-op in release builds**, so there is no red error screen — just an empty box with the title and actions still visible.

```dart
// WRONG — blank content on iOS, works in Chrome (sneaks past dev)
AlertDialog.adaptive(
  title: const Text('Staff Visibility'),
  content: SizedBox(height: 200, child: ListView(...SwitchListTile...)),
)

// CORRECT — plain Material AlertDialog renders on every platform
AlertDialog(
  title: const Text('Staff Visibility'),
  content: SizedBox(height: 200, child: ListView(...SwitchListTile...)),
)
```

If a Cupertino look is genuinely required for a specific dialog, wrap `content` in a `Material` widget so the Material children have an ancestor — but the default is the plain Material `AlertDialog`.

(Locked 2026-06-24 after the AstraOne CABS "Staff Visibility" dialog rendered blank on iPhone in QA while working in Chrome dev — `astraone_app/.../additional_resources`.)

## Submit Sequence (exact order)

1. Disable the action button (and Cancel) + show busy state: a `LinearProgressIndicator` (4 px) directly under the dialog title + the disabled button relabelled to a busy label (`Saving…`, `Removing…`). **Never a `CircularProgressIndicator` inside the button** — loader order shimmer › linear › circular (owner 2026-10-09, memory `feedback_loader_hierarchy_shimmer_linear_circular`). Reserve the 4 px slot when idle so the title never jumps. PD reference impl: `PdDialogBusyTitle` / `PdFormDialog(busy:)` + `PdBusyFilledButton` (`dashboard_product_development/lib/components/templates/ui/pd_confirm_dialog.dart`).
2. Validate form / inputs
3. Call API
4. **Success: SnackBar FIRST -> then close dialog** (`Navigator.pop`) — never reverse
5. **Error: keep dialog open + show error SnackBar**

### Why SnackBar before pop?

Calling `Navigator.pop()` first deactivates the scaffold. Any SnackBar call after pop crashes with "deactivated widget's ancestor". The sub-app SnackBar utility (e.g. `FinanceSnackBar.getSnackBar(text)`) must NEVER receive a `context:` parameter — it forces `ScaffoldMessenger.of(context)` which crashes after pop.

```dart
// WRONG — crashes
Navigator.of(context).pop(true);
FinanceSnackBar.getSnackBar('Saved.');

// CORRECT — snackbar first, then pop
FinanceSnackBar.getSnackBar('Saved.');
Navigator.of(context).pop(true);
```

## Feedback Loop (mandatory)

1. Apply the button + submit sequence pattern above
2. Run `dart analyze` on the sub-app root — zero diagnostics
3. Launch app, open the dialog, test both success and error paths
4. Verify: snackbar appears, dialog closes (success) or stays open (error)
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] Dialog uses plain `AlertDialog` / `Dialog` — NO `.adaptive` constructor
- [ ] Action button is `FilledButton` or `FilledButton.icon` — at far right of `actions:`
- [ ] Cancel button is `TextButton` — immediately left of action button
- [ ] No `ElevatedButton`, `OutlinedButton`, or custom `InkWell` in dialog `actions:`
- [ ] Submit sequence: disable -> validate -> API call
- [ ] Busy state = linear bar under the title + disabled busy-label button — no spinner in the button
- [ ] Success path: SnackBar FIRST, then `Navigator.pop()`
- [ ] Error path: dialog stays open + error SnackBar
- [ ] Sub-app SnackBar utility used (not raw `ScaffoldMessenger`)
- [ ] No `context:` parameter passed to sub-app SnackBar utility

---

## /provider-registration — Full Pattern


# Provider Registration — 4-File Rule + Safe Service References

## The 4-File Rule

Every new provider MUST be registered in **ALL 4 files** — missing any one causes gray screen or `ProviderNotFoundException`:

1. `dashboard_<sub_app>/lib/main.dart` — sub-app production
2. `dashboard_<sub_app>/lib/main_dev.dart` — sub-app dev
3. `dashboard_app/lib/main.dart` — host app production
4. `dashboard_app/lib/main_dev.dart` — host app dev

### After creating any new service

Grep all 4 files to confirm both **import + provider entry** exist before marking done:

```bash
grep -n "MyNewService" dashboard_<sub_app>/lib/main.dart dashboard_<sub_app>/lib/main_dev.dart dashboard_app/lib/main.dart dashboard_app/lib/main_dev.dart
```

Each file must have:
1. `import 'package:dashboard_<sub_app>/components/.../my_new_service.dart';`
2. `ChangeNotifierProvider(create: (_) => MyNewService()),` in the `MultiProvider.providers` list

## Safe Service References in Widgets

**NEVER `context.read<>()` in `dispose()` or async methods** — the widget's `BuildContext` is deactivated after unmounting or after an `await`.

```dart
// WRONG — crashes with "deactivated widget's ancestor"
void dispose() {
  context.read<MyService>().removeListener(_listener);
  super.dispose();
}

// WRONG — crashes after await if widget unmounted
Future<void> _onSave() async {
  await someAsyncWork();
  final svc = context.read<MyService>(); // BuildContext may be dead
}

// CORRECT — save service as late final field
late final MyService _svc;

@override
void initState() {
  super.initState();
  _svc = context.read<MyService>();
  _svc.addListener(_onServiceUpdate);
}

@override
void dispose() {
  _svc.removeListener(_onServiceUpdate);
  super.dispose();
}

void _onServiceUpdate() {
  if (mounted) setState(() {});
}

Future<void> _onSave() async {
  await _svc.saveItem();
  if (!mounted) return;
  if (_svc.errorMsg.isNotEmpty) {
    showError();
  } else {
    showSuccess();
  }
}
```

## Feedback Loop (mandatory)

1. Create the new service file
2. Register in all 4 main files (import + provider entry)
3. Grep all 4 files to confirm registration
4. Run `dart analyze` on both the sub-app AND `dashboard_app` — zero diagnostics
5. Launch the app — verify no gray screen or `ProviderNotFoundException`
6. Only then check off the verification checklist items

## Verification Checklist

- [ ] Service registered in `dashboard_<sub_app>/lib/main.dart` (import + provider)
- [ ] Service registered in `dashboard_<sub_app>/lib/main_dev.dart` (import + provider)
- [ ] Service registered in `dashboard_app/lib/main.dart` (import + provider)
- [ ] Service registered in `dashboard_app/lib/main_dev.dart` (import + provider)
- [ ] Grep confirms both import + provider entry in all 4 files
- [ ] Widget saves service as `late final` field in `initState`
- [ ] `dispose()` uses the saved field — never `context.read<>()`
- [ ] Async methods use the saved field — never `context.read<>()` after `await`
- [ ] `_onServiceUpdate` checks `mounted` before `setState`
- [ ] Call site: `await _svc.method(); if (!mounted) return;`

---

## /snackbar-utility — Full Pattern


# SnackBar Utility — Sub-App Class Only

**ALWAYS use the sub-app utility class. NEVER raw `ScaffoldMessenger`.**

## Lookup Table

| Sub-app | Class | Import |
|---|---|---|
| `dashboard_business_development` | `BusinessDevelopmentSnackBar.getSnackBar(text)` | `package:dashboard_business_development/util/ui/snackbar.dart` |
| `dashboard_finance` | `FinanceSnackBar.getSnackBar(text)` | `package:dashboard_finance/util/ui/snackbar.dart` |
| `dashboard_academic` | *(check `util/ui/snackbar.dart`)* | — |
| Other sub-apps | *(check `util/ui/snackbar.dart` in sub-app)* | — |

On first use in a new sub-app: `grep util/ui/snackbar.dart` to find the class name.

## Usage

```dart
// WRONG
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: Text('Saved')));

// CORRECT
BusinessDevelopmentSnackBar.getSnackBar('Saved.');
```

- No `context` needed — uses the global `ScaffoldMessengerKey` wired in `main.dart`
- **NEVER** pass `context:` parameter to the utility — it forces `ScaffoldMessenger.of(context)` which crashes after pop

## SnackBar-Before-Pop Rule

In dialog save methods: show SnackBar FIRST, THEN `Navigator.of(context).pop()`.

Calling pop first deactivates the scaffold -> SnackBar crashes with "deactivated widget's ancestor".

```dart
// WRONG — crashes
Navigator.of(context).pop(true);
FinanceSnackBar.getSnackBar('Saved.');

// CORRECT
FinanceSnackBar.getSnackBar('Saved.');
Navigator.of(context).pop(true);
```

## Feedback Loop (mandatory)

1. Replace any raw `ScaffoldMessenger` calls with the sub-app utility
2. Verify SnackBar is called BEFORE any `Navigator.pop()` in the same method
3. Run `dart analyze` — zero diagnostics
4. Launch app, trigger the action — verify SnackBar appears correctly
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] Sub-app SnackBar utility used — not raw `ScaffoldMessenger`
- [ ] Correct class name for the sub-app (grep `util/ui/snackbar.dart`)
- [ ] No `context:` parameter passed to the utility
- [ ] SnackBar called BEFORE `Navigator.pop()` in save/delete methods
- [ ] Success path: SnackBar -> pop
- [ ] Error path: SnackBar shown, dialog stays open

---

## /state-management — Full Pattern


# State Management — Provider Pattern

**Provider** is the standard. Logic lives in `AstragenChangeNotifier`/`ChangeNotifier` — separate from widgets.

## 1:1 Backend Mapping

UI `dashboard_<name>` maps to Backend `astragen_core_dashboard_<name>`.

## No-Callback Service Methods

Service methods must be `Future<void>` + `notifyListeners()` at start AND end. Never accept `Function(bool)` callback parameters.

```dart
// WRONG — callback pattern
Future<void> doWork(Function(bool) onRefresh) async {
  // ... work ...
  onRefresh(true);
}

// CORRECT — Future<void> + notifyListeners
Future<void> doWork() async {
  _loadingStatus = LoadingStatus.progress;
  notifyListeners();
  // ... work ...
  _loadingStatus = LoadingStatus.completed;
  notifyListeners();
}
```

## Widget-Service Wiring

```dart
late final MyService _svc;

@override
void initState() {
  super.initState();
  _svc = context.read<MyService>();
  _svc.addListener(_onServiceUpdate);
  WidgetsBinding.instance.addPostFrameCallback((_) {
    _svc.loadData();
  });
}

@override
void dispose() {
  _svc.removeListener(_onServiceUpdate);
  super.dispose();
}

void _onServiceUpdate() {
  if (mounted) setState(() {});
}
```

## Call Site Pattern

```dart
// WRONG
service.doWork((refresh) {
  if (refresh) setState(() {});
});

// CORRECT
await _svc.doWork();
if (!mounted) return;
if (_svc.errorMsg.isNotEmpty) {
  showError();
} else {
  showSuccess();
}
```

## Modal Classes — No Raw Maps

- NEVER use `List<Map<String, dynamic>>` in service/UI signatures when a modal exists
- NEVER define local record types when a shared modal exists — import the shared class
- Modals live in `*_modal.dart` (cross-app -> `dashboard_shared`) with `fromJSON`, `clone()`, `toJson()`
- Before introducing a record/mirror -> grep `dashboard_shared/lib/util/modal/`

## Feedback Loop (mandatory)

1. Apply the no-callback + listener pattern
2. Run `dart analyze` — zero diagnostics
3. Launch app, trigger the service method — verify UI updates correctly
4. Unmount the widget (navigate away) during async operation — verify no "setState after dispose" crash
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] Service method returns `Future<void>` — no `Function(bool)` callbacks
- [ ] `notifyListeners()` called at start AND end of service method
- [ ] Widget saves service as `late final` field in `initState`
- [ ] `addListener` in `initState`, `removeListener` in `dispose`
- [ ] `_onServiceUpdate` checks `mounted` before `setState`
- [ ] Call site uses `await` + `if (!mounted) return` + error check
- [ ] No `context.read<>()` in `dispose()` or after `await`
- [ ] Modal classes used instead of raw `Map<String, dynamic>`
- [ ] `addPostFrameCallback` used for initial data load

---

## /responsiveness — Full Pattern


# Responsiveness — Adaptive Layout by Breakpoint

Every UI page MUST handle Mobile/Tablet/Desktop using `ScreenSize` from **dashboard_shared** inside `OrientationBuilder`/`LayoutBuilder`. **Adaptive layout, not scaling** — design each breakpoint independently.

## Breakpoint Table

| Page type | Mobile (`< 600px`) | Tablet (`600-1024px`) | Desktop (`> 1024px`) |
|---|---|---|---|
| Single table/list | Horizontal scroll + `Scrollbar` | Same | Same |
| Master -> Detail (1 level) | Full-screen list; tap -> push detail | Split list left (~40%), detail right (~60%) | Split list left (~35%), detail right (~65%), selected row highlighted |
| Master -> Sub-list -> Detail (2 levels) | Full-screen list; push sub-list; `DraggableScrollableSheet` for detail | Primary list + sub-list side-by-side; detail as side sheet/dialog | All three panels side-by-side (~300-400/300-400/remainder) |
| Form / dialog | Full-screen page or `DraggableScrollableSheet` | Centred modal (max 560px) | Centred modal (max 600px) or end-drawer |
| Dashboard / cards | Single-column `Wrap`/`ListView` | 2-column `Wrap`/`GridView` | 3-4 column `Wrap`/`GridView` |

## Rules

- **Panel width rule:** each panel needs ~400-600px -> count panels that fit to pick tier
- **Bottom sheet on mobile; side sheet/end drawer/dialog on tablet+desktop**
- **Push new route on mobile** when content can't fit side-by-side

## Implementation Pattern

```dart
@override
Widget build(BuildContext context) {
  return OrientationBuilder(
    builder: (context, orientation) {
      return LayoutBuilder(
        builder: (context, constraints) {
          final screenSize = ScreenSize.fromConstraints(constraints);
          if (screenSize.isMobile) return _buildMobile();
          if (screenSize.isTablet) return _buildTablet();
          return _buildDesktop();
        },
      );
    },
  );
}
```

## Feedback Loop (mandatory)

1. Implement all three breakpoint layouts
2. Run `dart analyze` — zero diagnostics
3. Launch app on Chrome, resize browser to mobile width (<600px) — verify layout
4. Resize to tablet (600-1024px) — verify split panels or adjusted layout
5. Resize to desktop (>1024px) — verify full multi-panel layout
6. Only then check off the verification checklist items

## Verification Checklist

- [ ] `ScreenSize` from `dashboard_shared` used — not custom breakpoints
- [ ] `OrientationBuilder` / `LayoutBuilder` wraps the page
- [ ] Mobile layout: single-column, push navigation, bottom sheets
- [ ] Tablet layout: split panels or adjusted grid
- [ ] Desktop layout: full multi-panel or wide grid
- [ ] Each panel has ~400-600px minimum width
- [ ] Forms: full-screen on mobile, centered modal on tablet/desktop
- [ ] Cards: 1-col mobile, 2-col tablet, 3-4-col desktop

---

## /access-guard-tabs — Full Pattern


# AccessGuard Tab Access Skill — Role-Based Tab Filtering

Every tabbed page MUST load its tabs through `AccessGuard` filtering — NEVER directly from static data.

## Architecture

- `AccessGuard.getAccessibleTabs<T>()` — shared utility in `dashboard_shared/lib/util/access_guard.dart`
- Each sub-app has a convenience wrapper: `*AppModuleStaticData.getAccessibleTabs(pageName)` in its `drawer_service.dart`
- `dashboard_app` pages (Reports, Calendar, Profile) call `AccessGuard.getAccessibleTabs` directly with `AppLevelStaticInfo.dashboardPagesByRole`
- **Dev environment**: bypass (returns all tabs). **QA/Prod**: enforces role-based filtering.

## When creating a new tabbed page in a sub-app

```dart
// ✅ CORRECT — filtered through AccessGuard
tabList.addAll(XxxAppModuleStaticData.getAccessibleTabs('Page Name'));

// ❌ WRONG — bypasses role-based access control
tabList.addAll(page.tabDetails);
```

## When adding a new tab to a page's static data (`drawer_service.dart`)

1. Add the tab entry to the page's `tabDetails` list in `*AppModuleStaticData`
2. Set `appModuleId` if the tab has one in the `app_module` DB table (used for primary matching)
3. Tab filtering uses **name matching** — ensure the `tabName` enum label matches the backend `app_module.name` exactly
4. The tab will only be visible to roles that have a corresponding entry in `dashboardPagesByRole` (populated from `GET_ROLE_BASED_ACCESS_FOR_ALL_APPS` at login)
5. `appModuleId` is reserved for module-level (Add/Edit/View) control only — never used for page or tab filtering

## When creating a new tabbed page in `dashboard_app`

```dart
// dashboard_app pages don't have a sub-app wrapper — call AccessGuard directly:
_tabList = AccessGuard.getAccessibleTabs<YourTabDetails>(
  rolePages: AppLevelStaticInfo.dashboardPagesByRole,
  pageName: 'Page Name',
  allTabsForPage: allTabs,
  getTabName: (tab) => tab.tabName,
);
```

## Feedback Loop (mandatory)

1. Add tab via `getAccessibleTabs()` — never direct `tabDetails`
2. Run `dart analyze` on the sub-app root — zero diagnostics
3. Launch app in dev mode — verify all tabs visible (bypass active)
4. Confirm `tabName` matches `app_module.name` in DB
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] Page calls `getAccessibleTabs(...)` — never `page.tabDetails` directly
- [ ] `tabName` enum label matches `app_module.name` in DB exactly
- [ ] `appModuleId` set only if entry exists in `app_module` table
- [ ] `appModuleId` NOT used for filtering — only for module-level Add/Edit/View
- [ ] In dev mode: all tabs visible (bypass active)
- [ ] In QA/prod mode: only tabs with matching `dashboardPagesByRole` entry visible

---

## /search-bar-pattern — Full Pattern


# Search Bar Pattern — Local-First + Debounced API + Focus Dropdown

Every search bar on a list page MUST follow this pattern — **no exceptions**.

## The 7 Rules

1. **Local filter from 1st character** — filter the already-loaded list client-side instantly (`_filterLocally()`). Consumer uses the filtered result, not the raw list.
2. **2-char minimum for API calls** — never hit the backend for 0–1 character queries.
3. **500ms debounce** — wait 500ms after the last keystroke before firing the API call. Use `Timer` with cancel-on-each-keystroke.
4. **Empty query → reload full list** — clearing the search field calls the API with no filter to restore full data.
5. **Show latest** — when the API response arrives, `notifyListeners()` triggers Consumer rebuild; local filter re-applies on the fresh data automatically.
6. **Focus dropdown (on click)** — attach a `FocusNode` to the search field. When focused with empty text, render a dropdown below the field showing: `Recent` section (≤5 items from a per-user vault store) + `All` section (top 10 already-loaded items). When user types, the same dropdown narrows to local filter matches.
7. **Save top 5 recents globally** — on entity selection (tap row / edit), persist the entity's UUID to `AgSecureVault` under a per-user key (e.g. `aws_recent_<scope>_<empDbId>`). Dedupe, insert at top, cap at 5. Reuse a shared `RecentSearchStore` utility rather than reimplementing per page.

## Critical Distinctions

- **2-char minimum is ONLY for API debounce** — never applies to in-memory list filters. In-memory filter runs from the 1st character.
- **Empty tap → show recents** first, then alphabetical. Use `\u200B` zero-width trick if needed to trigger the focus dropdown on empty selection.
- **Reuse `RecentSearchStore`** — do not reimplement per page. See `AppLevelStaticInfo.recentSchools` pattern for global shared implementation.

## Feedback Loop (mandatory)

1. Implement the 7 rules above in the service + widget
2. Run `dart analyze` on the sub-app root — zero diagnostics
3. Launch app, navigate to the list page — type 1 char and verify instant local filter
4. Type 2+ chars, wait 500ms — verify API fires (check network tab)
5. Clear search — verify full list reloads
6. Focus empty field — verify Recents + All dropdown appears
7. Only then check off the verification checklist items

## Verification Checklist

- [ ] Consumer reads filtered result, not raw list
- [ ] `_filterLocally()` runs on every keystroke from 1st char
- [ ] API debounce uses `Timer` with cancel-on-each-keystroke, 500ms
- [ ] API fires only when query length >= 2
- [ ] Empty query triggers full-list reload
- [ ] `FocusNode` attached; dropdown shows Recents + All on empty focus
- [ ] On row selection: `RecentSearchStore.save(uuid)` — dedupe, top, cap at 5
- [ ] Per-user vault key: `<feature>_recent_<scope>_<empDbId>`

## Reference Implementations

- School search recent history: `AppLevelStaticInfo.recentSchools` / `loadRecentSchools()` / `saveRecentSchool()` in `dashboard_shared`
- AWS recent search store: `aws/util/aws_recent_search_store.dart`

---

## /filter-refresh — Full Pattern


# Filter/Search Refresh — No Full-Page Blank

When a filter chip or search bar triggers a re-fetch, the page must NOT go blank. Only the list and summary cards should update.

## Service Contract

```dart
Future<void> loadList({...}) async {
  _errorMsg = '';
  if (_list.isNotEmpty) {
    _isListRefreshing = true;           // subsequent loads: keeps existing list visible
  } else {
    _listStatus = LoadingStatus.progress; // first load: blank spinner is OK
  }
  notifyListeners();
  try { /* API call */ }
  finally { _isListRefreshing = false; notifyListeners(); }
}
```

## UI Layout Rules

1. **Top bar**: outside `Consumer` — uses state's `svc` (`Provider.of(listen: false)`) for callbacks.
2. **Content `Consumer`**: gates on `listStatus == start || (progress && list.isEmpty)` for first-load spinner only. When `isListRefreshing == true`, show shimmer rows (not a spinner).
3. **Bottom totals bar**: own `Consumer` so it never triggers a column rebuild.
4. Wrap each independently-updating section in `RepaintBoundary`.

## Shimmer Skeleton (custom AnimationController — no external package)

- `SingleTickerProviderStateMixin` with `_ctrl..repeat()` at 1300ms
- `AnimatedBuilder` → `LinearGradient(begin: Alignment(2*v-2, 0), end: Alignment(2*v, 0), colors: [base, highlight, base])`
- Renders 6–8 skeleton `Container`s at tile height

## Animated Summary Card Numbers (`TweenAnimationBuilder<double>`)

- `tween: Tween(begin: 0.0, end: value)`, duration 700ms, `Curves.easeOutCubic`
- On `end` change, Flutter animates from current position to new value automatically
- Apply to: totals, counts, `LinearProgressIndicator(value: ratio)` bars

## Animated Filter Chips (`AnimatedContainer`)

- Replace `Container` with `AnimatedContainer(duration: 220ms, curve: Curves.easeInOut)` on all status/filter chips

## errorMsg Success-Message Page Replacement Bug

**Critical:** Clear `errorMsg` in `_silentRefresh` before `setState`. Service sets `errorMsg = response.msg` even on 200 for some endpoints. Uncleared → `build()` shows success string as full-page content instead of list.

## Feedback Loop (mandatory)

1. Implement the service `_isListRefreshing` flag + shimmer UI
2. Run `dart analyze` — zero diagnostics
3. Launch app, load data, then change a filter — verify page does NOT go blank
4. Verify shimmer rows appear during refresh (not a spinner)
5. Verify summary cards animate to new values
6. Only then check off the verification checklist items

## Verification Checklist

- [ ] Service has `_isListRefreshing` flag set when `_list.isNotEmpty`, cleared in `finally`
- [ ] First-load blank spinner path only triggers when list is empty
- [ ] Top bar outside content Consumer — uses `Provider.of(listen: false)`
- [ ] Content Consumer gates correctly: `listStatus == start || (progress && list.isEmpty)`
- [ ] Shimmer rows render when `isListRefreshing == true` (not a spinner)
- [ ] Bottom totals have their own Consumer
- [ ] `RepaintBoundary` wraps each independently-updating section
- [ ] Summary card numbers use `TweenAnimationBuilder<double>` 700ms easeOutCubic
- [ ] Filter chips use `AnimatedContainer` 220ms easeInOut
- [ ] `errorMsg` cleared in silent refresh before setState

---

## /breadcrumb-overlay — Full Pattern


# Hierarchical Group Selector — Breadcrumb Overlay Pattern

**Never use a flat `DropdownButton` or `DropdownButtonFormField` for items that have a parent → child relationship** (e.g. `TeamGroup.subGroups`, org trees, zone hierarchies). A flat list destroys the hierarchy; always use the breadcrumb overlay pattern.

## Trigger

`InkWell` + full-width `Container` (NOT `ActionChip` — chips self-size and won't stretch). Show the deepest selected group name; "All `<Items>`" when nothing is selected. Put a close `×` icon next to it when a selection exists.

## Overlay

`CompositedTransformTarget` + `OverlayPortal` + `CompositedTransformFollower`. Set `maxWidth` wider than the parent widget (e.g. 440px) — overlays are unconstrained by their parent.

## Anchor direction — ALWAYS choose the side that keeps the panel inside the viewport (MANDATORY)

| Trigger position | `targetAnchor` | `followerAnchor` | `Align` | Panel grows |
|---|---|---|---|---|
| Near / at right edge (toolbar button, right-side drawer) | `Alignment.bottomRight` | `Alignment.topRight` | `Alignment.topRight` | ← leftward ✅ |
| Near / at left edge (form field, left panel) | `Alignment.bottomLeft` | `Alignment.topLeft` | `Alignment.topLeft` | → rightward ✅ |

**Rule of thumb:** pin the follower's corner that is closest to the nearest viewport edge — the panel then expands away from the edge and can never clip. When in doubt, right-anchor (`bottomRight/topRight`) is the safer default for toolbar buttons because toolbars are full-width and buttons typically sit toward the right.

## Panel Content

Horizontal-scroll `Row` with:
1. `AnimatedContainer` breadcrumb pill — "All Groups" root (always shown)
2. `chevron_right` + pill for each level in `_path` (tapping navigates back)
3. Separator line + `ActionChip` for each item in `_currentLevel` (tapping drills in)

## Local State (no service change needed)

```dart
final _overlayCtrl = OverlayPortalController();
final _layerLink = LayerLink();
final List<TeamGroup> _path = [];

List<TeamGroup> get _currentLevel => _path.isEmpty ? _topLevel : _path.last.subGroups;

void _navigateInto(TeamGroup g) {
  setState(() => _path.add(g));
  onChanged(g.id, g.description);
  if (g.subGroups.isEmpty) _overlayCtrl.hide(); // auto-close on leaf
}
void _navigateToIndex(int i) { // i < 0 = root
  setState(() => i < 0 ? _path.clear() : _path.removeRange(i + 1, _path.length));
  onChanged(i < 0 ? -1 : _path[i].id, i < 0 ? 'All' : _path[i].description);
}
```

## Data Source Rule (AWS specifically)

**All AWS area group dropdowns/filters MUST use `acdAwsTrainingGroupsAll`**, never `implementationGroups` or `acdAreaGroups`. Training Groups tab = write side; `acdAwsTrainingGroupsAll` = read-only login snapshot.

## Reference Implementations

- `aws_stats_sidebar.dart` → `_buildZoneDropdown` / `_buildZonePanel` / `_zoneBreadcrumbPill`
- `school_visits.dart` → `_teamsChip` / `_teamsSelectionPanel` / `_breadcrumbPill`
- `aws_scheduler_page.dart` — uses `acdAwsTrainingGroupsAll`
- `wizard_step1_products.dart` — another implementation reference

## Feedback Loop (mandatory)

1. Implement the overlay with breadcrumb navigation
2. Run `dart analyze` — zero diagnostics
3. Launch app, click the trigger — verify overlay appears correctly anchored
4. Drill into a sub-group — verify breadcrumb pills update
5. Select a leaf node — verify overlay auto-closes
6. Navigate back via breadcrumb pill — verify level changes
7. Only then check off the verification checklist items

## Verification Checklist

- [ ] Trigger is `InkWell` + full-width `Container`, NOT `ActionChip`
- [ ] Anchor direction matches viewport edge (right-edge → bottomRight/topRight)
- [ ] `maxWidth` on panel wider than parent (typically 440px)
- [ ] `_path` list tracks breadcrumb depth
- [ ] `_navigateInto` auto-closes overlay when `subGroups.isEmpty` (leaf)
- [ ] `_navigateToIndex(-1)` clears path to root; `i >= 0` trims to that level
- [ ] `onChanged` callback fires with id + description
- [ ] Deepest selected name shown on trigger; close × when selection exists
- [ ] For AWS: data source is `acdAwsTrainingGroupsAll` (not implementationGroups)

---

## /date-pickers — Full Pattern


# Date/Time Pickers — MANDATORY (no exceptions)

**Never use `showDatePicker`, `showTimePicker`, or `showDateRangePicker`.** Always use the `AgInline*` picker family from `package:astragen_flutter_utility/ag_date_picker/`. These are inline trigger + anchored-overlay widgets that auto-flip, auto-scroll, integrate with Form validation, and match M3 theme.

## Date & Time Standards

- **User Display:** `dd/MM/yy` (e.g., 27/02/26)
- **API/Database:**
  - `yyyy-MM-dd` → date-only fields
  - `yyyy-MM-ddTHH:mm:ss` → timestamp fields (ISO 8601)
- **Consistency:** Ensure conversion logic between UI ↔ API

## Variants

- **Non-form (filter buttons, settings, list-add):**
  - `AgInlineDatePicker`
  - `AgInlineDateTimePicker`
  - `AgInlineDateRangePicker`
  - `AgInlineDateTimeRangePicker`
- **Form-field wrappers (inside `Form`):**
  - `AgInlineDatePickerFormField`
  - `AgInlineDateTimePickerFormField`

## Critical API Gotchas

| Parameter | Non-form pickers | Form-field wrappers |
|---|---|---|
| Initial value param name | `value:` | `initialValue:` |
| Callback | `onSelected` / `onRangeSelected` | `onChanged` |
| Callback DateTime nullability | **NON-nullable** (no null guards) | **Nullable** (null check IS correct) |

- ❌ Non-form: adding `if (picked != null)` → causes `unnecessary_null_comparison` lint
- ✅ Form-field: `if (picked != null) { ... }` IS correct
- Preserve `firstDate`/`lastDate` exactly — they encode business rules
- Preserve cross-field interlocks (e.g. `endPicker.firstDate = startDate`)

## Reference Files (copy from)

- **Form-field (date + date+time with ISO-string modal fields):**
  `dashboard_app/lib/components/tickets/view_ticket_description/form_implementation_call/implementation_call_ui.dart`
- **List-add pattern** (pick → push to list → chips) with `FormField<int>` "at least one" validation:
  `dashboard_app/lib/components/tickets/view_ticket_description/form_sv_school_inform/sv_school_inform_ui.dart`
- **Date range:**
  `dashboard_admin/lib/components/master_data/master_admin_page/work_ticket_categories/work_ticket_categories.dart`

## TimeOfDay-Only Exception

No inline `TimeOfDay` form-field exists yet. `showTimePicker` calls with no adjacent date picker may stay on legacy until a `AgInlineTimePickerFormField` is added to the utility package.

## Feedback Loop (mandatory)

1. Replace any `showDatePicker`/`showTimePicker` with the AgInline* variant
2. Run `dart analyze` — zero diagnostics (watch for `unnecessary_null_comparison`)
3. Launch app, open the picker — verify it renders inline, not as a dialog
4. Select a date — verify the value propagates (check form field or state)
5. Test cross-field interlocks if applicable (end.firstDate = start)
6. Only then check off the verification checklist items

## Verification Checklist

- [ ] No `showDatePicker` / `showTimePicker` / `showDateRangePicker` in the code
- [ ] Correct variant chosen: form-field wrapper inside `Form`, non-form elsewhere
- [ ] Initial value uses `value:` (non-form) or `initialValue:` (form-field)
- [ ] Non-form `onSelected`: NO null guard (params are non-nullable)
- [ ] Form-field `onChanged`: null check present (params are nullable)
- [ ] `firstDate`/`lastDate` preserved from business rules
- [ ] Cross-field interlocks set (e.g. end.firstDate = start)
- [ ] User display uses `dd/MM/yy`; API uses `yyyy-MM-dd` or ISO 8601

---

## /custom-colors — Full Pattern


# Custom Colors — DashBoardColors (ZERO HARDCODED HEX)

## Priority Order

1. **M3 semantic colors** -> `Theme.of(context).colorScheme.*` (primary, surface, error, outline, etc.)
2. **App-wide custom colors** -> `context.customColors.propertyName`

## Currently Registered Custom Colors

| Property | Purpose |
|---|---|
| `success` | Success state |
| `warning` | Warning state |
| `info` | Informational state |
| `error` | Error state (supplement to colorScheme.error) |
| `sidebarBackground` | Sidebar/drawer background |
| `columnHeaderBackground` | Table column headers |

**File:** `vs-astra_other/dashboard_shared/lib/util/ui/theme.dart`
**Import:** `package:dashboard_shared/util/ui/theme.dart`

## To Add a New Custom Color

1. Add `final Color yourColor;` field to `DashBoardColors`
2. Update `copyWith` override
3. Update `lerp` override
4. Supply light + dark values in `DashBoardTheme.getLightTheme()` / `getDarkTheme()`

## Usage

```dart
// WRONG
Container(color: const Color(0xFFF5EFE6))

// CORRECT — M3 semantic
Container(color: Theme.of(context).colorScheme.surfaceContainerHighest)

// CORRECT — custom color
Container(color: context.customColors.sidebarBackground)
```

## Feedback Loop (mandatory)

1. Check if M3 `colorScheme` already has the color you need
2. If not, add to `DashBoardColors` (field + copyWith + lerp + light/dark values)
3. Run `dart analyze` on `dashboard_shared` — zero diagnostics
4. Launch app — verify the color renders correctly in both light and dark theme
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] No `Color(0xFF...)` or `Color.fromARGB` in widget build methods
- [ ] M3 `colorScheme` checked first for existing semantic color
- [ ] If custom: field added to `DashBoardColors`
- [ ] `copyWith` and `lerp` updated
- [ ] Light + dark values supplied in `getLightTheme()` / `getDarkTheme()`
- [ ] Access via `context.customColors.propertyName`
- [ ] Import: `package:dashboard_shared/util/ui/theme.dart`

---

## /api-body-pattern — Full Pattern


# Flutter API Call Body — Required Pattern (MANDATORY)

Every `apiCallWithAuth` call MUST follow this exact structure — no exceptions.

## Why `.toString()` on every numeric is non-negotiable

Backend `util/get_params.js` (every dept Lambda — never modified) validates required params with:

```js
for (var i = 0; i < reqParams.length; i++) {
  var item = reqParams[i];
  if (data[item]) {           // ← JS truthy check
    responseData.push(data[item]);
  } else {
    isDataPresent = false;
  }
}
```

**JS truthy treats `0` as falsy.** Send `'page_offset': 0` from Flutter → backend sees missing param → returns `205 InSufficient Data`. The IT support-dispatch page hit this on 2026-05-28: Flutter sent `pageOffset: 0` (default first-page offset), backend rejected with 205. Fix is one-line on Flutter: `.toString()`.

Rule: **every int/double body value gets `.toString()` — required or optional, zero-valued or not, no exceptions.** Backend `get_params.js` is the contract; it is NOT changed.

## Flutter Side

```dart
final String body = jsonEncode({
  // Required params — always include as-is
  'param_name': value.toString(),

  // Optional params — ALWAYS include with sentinel defaults (never use inline `if` to omit keys)
  'uuid_field':   uuidField.isNotEmpty   ? uuidField   : 'NA',    // optional UUID/string
  'string_field': stringField.isNotEmpty ? stringField : 'NA',    // optional text
  'int_field':    intField > 0           ? intField.toString() : '-1',    // optional int ID/count
  'double_field': doubleField > 0        ? doubleField.toStringAsFixed(2) : '-1.0', // optional amount
  'search_field': searchField.isNotEmpty ? searchField : 'NA',    // optional search
});
await XxxHttpService.apiCallWithAuth('handler/METHOD', body);
```

### Flutter Rules

1. `body` is always `final String body = jsonEncode({...})` — never `Map`, never `jsonEncode(jsonEncode(...))`.
2. Optional params are **always present** in the JSON with a sentinel default — never use `if (x.isNotEmpty) 'key': x` conditional inclusion.
3. Sentinel defaults: string/UUID → `'NA'`, int → `'-1'`, double → `'-1.0'`, search → `'NA'`.
4. `bool` params always serialized as `'TRUE'` / `'FALSE'` strings.
5. **NEVER** pass raw `int` — always `.toString()`. For nullable int: `id?.toString() ?? '-1'`.
6. For list parameters (e.g., `travel_expenses`, `support_files`), always pass an empty array `[]` — omitting triggers `InSufficient Data` (Code 205).

## Backend Side — Parameter Handling

### Never use `JSON.parse(event.body)`

- **ALL parameters** — mandatory, optional, and nullable — **MUST** go through `getParams.GET_PARAMS()` in the `reqParams` array.
- For optional/nullable fields (e.g. `uuid` empty = create, non-empty = update; `content`/`file_url` may be empty string): include them in `reqParams` and handle the empty/null at extraction time via `getParamsResponse.msg[i]?.toString() ?? ""`.

```js
// ✅ CORRECT
var reqParams = ["uuid", "name", "content"];
// ... after GET_PARAMS ...
var uuid = getParamsResponse.msg[0]?.toString() ?? "";

// ❌ WRONG — FORBIDDEN
var uuid = JSON.parse(event.body)["uuid"];
```

### Backend Parsing Guards (every `GET_NON_MANDATORY_PARAMS` field)

```js
var optionalResponse = getParams.GET_NON_MANDATORY_PARAMS(event, ['uuid_field', 'str_field', 'int_field', 'double_field', 'search_field'], logMsg);
logMsg = optionalResponse.logMsg;
var [uuidField, strField, intField, doubleField, searchField] = optionalResponse.msg;

// UUID/string optional — 'NA' means not provided → null in DB
var parsedUuid    = uuidField !== 'NA' ? uuidField : null;   // create vs update: check === 'NA'
var parsedStr     = strField  !== 'NA' ? strField  : null;   // text → null if NA

// Int optional — '-1' sentinel; guard both 'NA' and negative values
var parsedInt     = (intField !== 'NA' && !isNaN(parseInt(intField)) && parseInt(intField) > 0)
                    ? parseInt(intField) : null;             // ID/count: must be > 0
var parsedCount   = (intField !== 'NA' && !isNaN(parseInt(intField)) && parseInt(intField) >= 0)
                    ? parseInt(intField) : 0;                // count with 0 default: must be >= 0

// Double optional — '-1.0' sentinel; guard both 'NA' and negative values
var parsedDouble  = (doubleField !== 'NA' && !isNaN(parseFloat(doubleField)) && parseFloat(doubleField) >= 0)
                    ? parseFloat(doubleField) : null;        // amount → null if not provided

// Search optional — 'NA' means no filter
if (searchField !== 'NA') {
    filters += ' AND col LIKE ?';
    values.push('%' + searchField + '%');
}
```

### Backend Guard Summary

| Field type | Guard |
|---|---|
| String/UUID | `!== 'NA'` check only |
| Int ID (FK, roomTypeId, etc.) | `!== 'NA' && !isNaN(parseInt(x)) && parseInt(x) > 0` |
| Int count (expectedCount, paxCount, etc.) | `!== 'NA' && !isNaN(parseInt(x)) && parseInt(x) >= 0` |
| Double amount | `!== 'NA' && !isNaN(parseFloat(x)) && parseFloat(x) >= 0` |
| Search filter | `!== 'NA'` check only |
| Create vs update (UUID) | `=== 'NA'` → INSERT path, else → UPDATE path |

### Bool Handling

For booleans, always pass stringified values `"TRUE"` or `"FALSE"`, as backend comparison logic relies on strict checks (e.g., `msg[5] === 'TRUE'`).

## Feedback Loop (mandatory)

1. Write the Flutter body with sentinel defaults and the backend guards
2. Run `dart analyze` — zero diagnostics
3. Test via `test_runner.js` — confirm handler accepts the sentinels correctly
4. Send a request with missing optional params (sentinels) — confirm no 205 error
5. Send a request with valid optional params — confirm they're processed
6. Only then check off the verification checklist items

## Verification Checklist

### Flutter
- [ ] `body` is `final String body = jsonEncode({...})` — not Map, not double-encoded
- [ ] Every optional param has a sentinel default (no conditional `if`-inclusion)
- [ ] String/UUID default `'NA'`, int default `'-1'`, double default `'-1.0'`
- [ ] `int` always `.toString()` — nullable: `id?.toString() ?? '-1'`
- [ ] `bool` serialized as `'TRUE'` / `'FALSE'`
- [ ] List params pass `[]` when empty (never omitted)

### Backend
- [ ] All params read via `getParams.GET_PARAMS()` or `GET_NON_MANDATORY_PARAMS()`
- [ ] NO `JSON.parse(event.body)` anywhere
- [ ] Int ID guard: `!== 'NA' && !isNaN(parseInt(x)) && parseInt(x) > 0`
- [ ] Int count guard: `!== 'NA' && !isNaN(parseInt(x)) && parseInt(x) >= 0`
- [ ] Double guard: `!== 'NA' && !isNaN(parseFloat(x)) && parseFloat(x) >= 0`
- [ ] Search filter: `!== 'NA'` check only
- [ ] UUID create/update: `=== 'NA'` → INSERT, else UPDATE
- [ ] Control flow uses braces `{}` even on single lines
- [ ] Endpoint string: FULL UPPERCASE, `handler_name_without_handler_suffix/METHOD_NAME`

---

## /api-versioning — Full Pattern


# API Versioning — When to Create a New Version

Never silently modify an existing endpoint's contract. Breaking change -> suffix `_V2`, `_V3`, etc.

## Decision Table

| Breaking (new version REQUIRED) | Non-breaking (modify in place) |
|---|---|
| New required param | New optional param with safe default |
| Required -> optional (semantics change) | New key in response (additive) |
| Rename / remove existing param | Pure internal refactor |
| Change param datatype/format (string date -> epoch, id -> uuid) | — |
| Rename / remove / restructure response key | — |
| Change response value type/format (String -> Number) | — |

## Process

1. Create new method with `_V2` / `_V3` suffix in the handler
2. Keep old method **active** with `// DEPRECATED — use GET_EVENT_LIST_V2` comment
3. Add new version to `_handler_test.http` with 1 happy + 2 edge cases
4. Update Flutter callers to use the new version
5. **NEVER delete, comment out, or deactivate the old version** — users on old app versions still hit it
6. Ask the user before any removal of deprecated endpoints

## Feedback Loop (mandatory)

1. Check the decision table — is this a breaking change?
2. If yes: create new versioned method, keep old active
3. Test new version via `test_runner.js` — confirm 200 response
4. Test old version still works — confirm backward compatibility
5. Update `_handler_test.http` with new version entries
6. Only then check off the verification checklist items

## Verification Checklist

- [ ] Breaking change identified using the decision table
- [ ] New method created with `_V2`/`_V3` suffix
- [ ] Old method stays active with `// DEPRECATED — use <NEW_VERSION>` comment
- [ ] New version tested via `test_runner.js` — 200 response
- [ ] Old version tested — still returns expected response
- [ ] `_handler_test.http` updated with 1 happy + 2 edge cases for new version
- [ ] Flutter callers updated to new version
- [ ] No deletion of old version without explicit user approval

---

## /api-safety — Full Pattern


# API Safety — 4 Critical Backend Rules

## 1. UUID Generation — Always `crypto.randomUUID()`

**`require('uuid')` is BANNED project-wide.** v9+ has no CJS export; breaks entire Lambda handler.

```js
// BANNED — latent Lambda crash
const { v4: uuidv4 } = require('uuid');

// CORRECT
const { randomUUID } = require('crypto');
// usage: randomUUID()
```

On sight of `require('uuid')` in any file: replace immediately.

## 2. SQL IN() — Spread Placeholders, Never String-Concat

```js
// WRONG — SQL injection risk
var sql = `WHERE id IN (${ids.join(',')})`;

// CORRECT — parameterized
var placeholders = ids.map(() => '?').join(',');
var sql = `WHERE id IN (${placeholders})`;
var values = [...ids]; // spread into bind values
```

Sanitize first: `ids = ids.map(Number).filter(n => !isNaN(n));`

## 3. forEach(async) — BANNED

`forEach` discards the promise. Response races DB work — returns 200 while INSERTs still pending.

```js
// WRONG — promises discarded
items.forEach(async (item) => {
  await mySQL.WRITE(insertSql, [item.id], creds, logMsg);   // v2 verb — never EXECUTE_QUERY
});

// CORRECT — awaits each iteration
for (const item of items) {
  await mySQL.WRITE(insertSql, [item.id], creds, logMsg);   // v2 verb — never EXECUTE_QUERY
}
```

Treat every `forEach(async` sighting as a bug, not style.

## 4. Number Parsing Guards

Never `Number()` / `parseInt()` / `parseFloat()` on unknown strings without guard:

```js
// WRONG — NaN propagates silently
const val = Number(str);

// CORRECT
const val = isNaN(parseFloat(str)) ? 0 : Number(str);
```

### Flutter -> API Body

- NEVER raw `int` — always `.toString()`
- Nullable int: `id?.toString() ?? '-1'`
- Bool: always `"TRUE"` / `"FALSE"` strings

## Feedback Loop (mandatory)

1. Search the handler for any of the 4 violations
2. Fix all instances
3. Test via `test_runner.js` — confirm no crashes
4. Grep the file for `require('uuid')`, `forEach(async`, bare `Number(` — should find zero
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] No `require('uuid')` — uses `require('crypto').randomUUID`
- [ ] SQL IN() uses `?` placeholders + spread values — no string concat
- [ ] No `forEach(async` — uses `for...of` with `await`
- [ ] `Number()` / `parseInt()` / `parseFloat()` guarded with `isNaN()` + default
- [ ] Flutter sends `.toString()` for ints, `"TRUE"`/`"FALSE"` for bools

---

## /handler-structure — Full Pattern


# Handler Structure, Helper Placement & DB Verbs

**Rule source:** `vs-astra_other/dashboard_shared/architecture/01-capabilities/reuse-layers.md` rule 2
(placement ladder) and `05-data-flow/mysql.md` rules 1–2, 8 (DB verbs). This skill is the applied
form; if they ever disagree, the architecture page wins — fix the skill.

## Handler File Structure

Every handler follows the `api-node-snippet` pattern:

```js
var getParams = require('../util/get_params');
var returnResponse = require('../util/return_response');
var mySQL = require('../util/run_mysql_script');
var creds = require('../env_variables');

var methods = {
  METHOD_NAME: async function (req, res) {
    var logMsg = 'METHOD_NAME: ';
    var reqParams = ['param1', 'param2'];
    var getParamsResponse = getParams.GET_PARAMS(req, reqParams, logMsg);
    if (getParamsResponse.code !== 200) {
      return returnResponse.RETURN_RESPONSE(res, getParamsResponse.code, getParamsResponse.msg, logMsg);
    }
    logMsg = getParamsResponse.logMsg;
    var [param1, param2] = getParamsResponse.msg;

    var rows = await mySQL.READ(sql, [param1], creds.DASHBOARD_X, logMsg);   // v2 verb
    logMsg = rows.logMsg;
    if (rows.code !== 200) {
      return returnResponse.RETURN_RESPONSE(res, rows.code, rows.msg, logMsg); // 500 = real DB error
    }
    // ... business logic ...
  },
};
module.exports = methods;   // ← MUST be exactly this. NOT `module.exports = { methods };`
```

## Lambda Export Rule (CRITICAL — silent runtime failure if wrong)

`index.js` resolves endpoints via `myHandler[taskName]`:

```js
const myHandler = require(handlerLocation);
var handlerResponse = await myHandler[taskName](event, credentials, logMsg);
```

So `module.exports` MUST be the `methods` object **directly**:

| Pattern | Result |
|---|---|
| ✅ `module.exports = methods;` | `myHandler.METHOD_NAME` = the function. Routing works. |
| ❌ `module.exports = { methods };` | `myHandler.METHOD_NAME` = `undefined`. Lambda throws `TypeError: myHandler[taskName] is not a function` at runtime. No compile-time check. |

The IT `support_handler.js` shipped to QA on 2026-05-28 with `module.exports = { methods };` — every call to `/it/support/*` 500'd until caught. There is no lint rule that catches this; if you copy a handler from another repo or LLM, double-check the export line.

## Helper Placement — the four-layer ladder (LOCKED with owner 2026-09-04)

Place every helper by its **reach**. Climb only as far as the reach demands; never park a
module-local helper in a shared layer "for later", and never inline an estate-wide helper in one
handler because it was convenient.

| Reach | Node home | Flutter home |
|---|---|---|
| **One tab / module** | module-private `function _xxx()` inside the handler, or a sibling `<module>_helper_functions.js` next to it in `handlers/` | the module's own folder under `lib/components/<module>/` |
| **One backend project / one sub-app** | that backend's `util/` (`UPPER_SNAKE_CASE` exports, JSDoc) | that sub-app's `lib/util/` |
| **Whole dashboard estate** (all `astragen_core_dashboard_*` / all sub-apps) | `util/internal_use_calls.js` — byte-identical ×10; edit the canonical copy, propagate, md5-verify | `dashboard_shared` |
| **Generic / long-running / cross-product** | shared utility Lambda, or the Auto-Scheduler Worker (hand over via a MySQL job row — never a direct invoke) | `astragen_flutter_utility` |

- Existing project-level util homes: `util/current_date_time.js` (`CURRENT_DATE`, `DB_DATETIME_NOW`…),
  `util/comon.js` (`COMPRESS_JSON_USING_ZLIB`…), `util/s3_storage.js`, `util/live_event_publisher.js`.
- Promotion up a layer is a **deliberate move named in the plan**, never a side effect. Additive
  versioning (`FN_V2`) applies from the estate layer up.
- This supersedes the older "util-first" (skill v2) and "handler-only, never touch util/" (memory
  2026-06-16) wordings — both were half of this ladder.

## DB access — v2 verbs ONLY (owner decision 2026-09-04)

| Verb | Routes to | Use for |
|---|---|---|
| `READ(sql, values, creds, logMsg)` | replica (master inside the 5s sticky window) | every plain SELECT |
| `READ_MASTER(sql, values, creds, logMsg)` | master | the read-after-write echo inlined in a write response |
| `WRITE(sql, values, creds, logMsg)` | master (stamps sticky window) | single INSERT / UPDATE / DELETE |
| `READ_BATCH({KEY: {sql, values}}, creds, logMsg)` | replica | several reads on one connection; partial failure → 500 + `failedKeys` (render `—`, never 0) |
| `TRANSACTION(statements, values, creds, logMsg)` | master | 2+ statements atomically — array OR semicolon-joined string + one flat values array; accepts a single statement too |

- **v2 error contract:** SQL failure → `code: 500` + stack in `logMsg`. Never treat 500 as "no rows".
- `EXECUTE_QUERY` / `EXECUTE_TRANSACTION` are **frozen-legacy**: never in new code; when you edit a
  method that still uses them, migrate that method's calls in the same change. The remaining ~10k
  legacy call sites are a tracked migration backlog (memory `project_rule_reconciliation_2026_09_04`).
- Full routing / sticky-window / cap reference: `mysql.md` Half 1.

## Endpoint Routing & Naming

- Format: `handler_filename_without_handler_suffix/METHOD_NAME`
- Example: `my_page_handler.js` + `API_1` -> `my_page/API_1`
- **NEVER** include `_handler` in the call string
- **FULL UPPERCASE** for endpoint method strings
- Always braces `{}` for ALL control flow — even single-line

## Parameter Handling — CRITICAL

- **NEVER** `JSON.parse(event.body)` or access `event.body` directly
- Mandatory params via `getParams.GET_PARAMS()`; optional via `getParams.GET_NON_MANDATORY_PARAMS()`
  (a truthy check — raw `0` / `''` read as missing, so Flutter `.toString()`s every numeric)

## Database Credentials

- `credentials.DASHBOARD_<PRODUCTNAME>` from `environment_variables.js`
- `.http` file JSON payload keys MUST exactly match `var reqParams = [...]` strings

## API Testing Workflow

- Every handler has `_handler_test.http` alongside it
- `test_runner.js` PREFERRED for single-method: `node test_runner.js <handler> <METHOD> '<json>'` — it never exits; never pipe it
- `httpyac --all` for full-file regression
- After any handler write/edit -> test immediately via `test_runner.js` before moving on

## Feedback Loop (mandatory)

1. Write the handler following the structure above
2. Place each helper on the ladder by reach; state the layer in the plan
3. Every DB call is a v2 verb; `logMsg` re-threaded after each
4. Test via `test_runner.js` — confirm 200 response (and that a forced SQL error returns 500, not 201)
5. Update `_handler_test.http` with 1 happy + 2 edge cases
6. Run `httpyac --all` for regression
7. Only then check off the verification checklist items

## Verification Checklist

- [ ] Handler follows `api-node-snippet` structure; `module.exports = methods;` direct
- [ ] Every helper sits at its reach layer (module / project `util/` / `internal_use_calls.js` / utility-worker) — none promoted "for later"
- [ ] Zero `EXECUTE_QUERY` / `EXECUTE_TRANSACTION` in written or edited methods
- [ ] Read-after-write echoes via `READ_MASTER`; atomic multi-statement via `TRANSACTION`
- [ ] Endpoint string: `handler_name_without_handler_suffix/METHOD` (FULL UPPERCASE)
- [ ] All params via `GET_PARAMS()` / `GET_NON_MANDATORY_PARAMS()` — no `JSON.parse(event.body)`
- [ ] `.http` payload keys match `reqParams` array exactly
- [ ] Tested via `test_runner.js` — 200 response confirmed
- [ ] `_handler_test.http` updated with 1 happy + 2 edge cases

---

## /db-read-replica — Full Pattern


# DB Master / Read-Replica — Read-After-Write Rule (v2 verbs)

**Rule source:** `vs-astra_other/dashboard_shared/architecture/05-data-flow/mysql.md` (Half 1
"Access methods — v2 verbs"; Half 2 rules 1, 2, 8). This skill is the applied form; the page wins on
any disagreement.

Two DB servers: **master** (writes) + **read replica** (reads). `READ` routes to the replica;
`READ_MASTER` / `WRITE` / `TRANSACTION` route to master. After any `WRITE`/`TRANSACTION` the util keeps
reads on master for a 5s **sticky window** — a safety net, not the contract. The contract is explicit.

## The Problem

A `READ` immediately after a write can land on the replica before replication catches up → stale or
missing rows. A second GET from Flutter after the save hits the same lag.

## The Solution: `READ_MASTER` inlined into the write response

```js
// CORRECT — write, then echo the fresh row from master, in ONE response
var ins = await mySQL.WRITE(insertSql, values, creds, logMsg);
logMsg = ins.logMsg;
if (ins.code !== 200) { return returnResponse.RETURN_RESPONSE(res, ins.code, ins.msg, logMsg); } // 500 = real error
var fresh = await mySQL.READ_MASTER(selectSql, [ins.msg.insertId], creds, logMsg);
logMsg = fresh.logMsg;
return returnResponse.RETURN_RESPONSE(res, 200, { MSG: 'Success.', BOOKINGS: fresh.msg }, logMsg);

// WRONG — UI then calls GET_BOOKINGS, which READs the replica and may miss the new record
return returnResponse.RETURN_RESPONSE(res, 200, { MSG: 'Success.' }, logMsg);
```

Use `READ_MASTER` **only** for the echo of what you just wrote (or a decision that must see the
latest committed state). Everything else is `READ` — routing every SELECT to master recreates the
2026-06-03/06-09 master-overload incidents.

## Atomic multi-statement writes: `TRANSACTION`

```js
// Array form (preferred — one statement per entry, one flat values array across all `?`)
await mySQL.TRANSACTION(
  ['INSERT INTO t1 (a) VALUES (?)', 'INSERT INTO t2 (b) VALUES (?)'],
  [val1, val2], creds, logMsg);

// Semicolon-joined string form is also accepted; so is a SINGLE statement
// (no more `'; SELECT 1;'` padding hack).
```

Any statement failing → rollback + `code: 500`.

## Legacy you will meet (do NOT write; migrate on touch)

| Legacy | Means | v2 |
|---|---|---|
| `EXECUTE_QUERY(sql, v, creds, logMsg)` | replica read (or master if the text sniffed as a write) | `READ` |
| `EXECUTE_QUERY(sql, v, creds, logMsg, true)` | forced master read | `READ_MASTER` |
| `EXECUTE_QUERY(insert/update…)` | write | `WRITE` |
| `EXECUTE_QUERY({KEY: sql, …})` (batch) | batch reads, `values` silently dropped, errors → `[]` in a 200 | `READ_BATCH` |
| `EXECUTE_TRANSACTION('a;b', v, …)` | atomic multi-statement | `TRANSACTION` |

Legacy returns **201** on a DB error — callers routinely misread it as "no rows". That is the
defect the v2 500 contract closes; it is why the pair is frozen.

## Flutter Side

When the write response carries data, splice it directly into local state. Remove any
`getXxx(isUpdateList: true)` following a write:

```dart
// WRONG — second call hits the replica
await _svc.saveBooking();
await _svc.getBookings(); // may miss the new record

// CORRECT — the save response already carries the fresh row(s)
await _svc.saveBooking(); // service splices response data into _bookings list
```

## Feedback Loop (mandatory)

1. Write the handler: `WRITE`/`TRANSACTION`, then `READ_MASTER` echo in the same response
2. Test via `test_runner.js` — confirm the response contains the fresh data key with the new values
3. Verify the Flutter service uses the inline data (no separate GET)
4. Only then check off the verification checklist items

## Verification Checklist

- [ ] Write response includes the inline `READ_MASTER` result: `{ MSG: 'Success.', <DATA_KEY>: [...] }`
- [ ] Plain SELECTs use `READ`, not `READ_MASTER`
- [ ] Multi-statement atomic writes use `TRANSACTION` (array or joined string + flat values)
- [ ] Zero `EXECUTE_QUERY` / `EXECUTE_TRANSACTION` in written or edited methods
- [ ] DB error path returns the util's 500 unchanged (never remapped to 201)
- [ ] No separate GET call from Flutter after a write; no `getXxx(isUpdateList: true)` after save/update

---

## /code-column-rules — Full Pattern


# `code` Column Rules — Stable Identifier Pattern

**Display column is ALWAYS `description`, NOT `name`** — applies to ALL master/lookup tables.

## Column Definition

- Type: `VARCHAR(6)`, `NOT NULL`, no default empty string
- Stores short 3-6 char codes: `PHD`, `MIX`, `PAT`, `PPI`
- **Codes, not IDs:** IDs change across dev/qa/prod during migrations; short codes stay stable

## Backend Rules

- Always filter/check by `code`, never by `id`, in business logic and SQL
- `code` always in `GET_PARAMS`, NEVER `GET_NON_MANDATORY_PARAMS`
- Uniqueness check before INSERT:
  ```sql
  SELECT COUNT(*) FROM table WHERE code = ? AND <scope_cols> AND status = 'ACTIVE'
  ```
  Return 205 if count > 0
- Uniqueness check before UPDATE: same query with `AND uuid/id != <current_record>` (exclude self)
- Scope: natural business scope (e.g. `financial_year_id + product_id` for policy tables; globally for pure master/lookup)

## Flutter Rules

- Service: send `code.toUpperCase()` — never `'NA'` as a code value
- Fetch master data in master API; save as local enum or validate after GET
- Drive all logic from `CODE` field, not `ID`

### Flutter UI for Code Field

```dart
TextFormField(
  decoration: const InputDecoration(labelText: 'Code *'),
  textCapitalization: TextCapitalization.characters,
  inputFormatters: [
    FilteringTextInputFormatter.allow(RegExp(r'[A-Za-z0-9_]')),
    LengthLimitingTextInputFormatter(6),
  ],
  validator: (v) => (v == null || v.isEmpty) ? 'Code is required' : null,
)
```

## Feedback Loop (mandatory)

1. Define the code column as `VARCHAR(6) NOT NULL`
2. Add uniqueness check in backend (INSERT and UPDATE paths)
3. Add code field to Flutter form with validation + formatters
4. Test via `test_runner.js` — confirm duplicate code returns 205
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] Column: `VARCHAR(6)`, `NOT NULL`, no default empty string
- [ ] Display column is `description`, not `name`
- [ ] Backend filters by `code`, not `id`
- [ ] `code` in `GET_PARAMS` (mandatory), not `GET_NON_MANDATORY_PARAMS`
- [ ] INSERT uniqueness: `SELECT COUNT(*) WHERE code = ? AND <scope> AND status = 'ACTIVE'`
- [ ] UPDATE uniqueness: same + `AND uuid/id != ?`
- [ ] Flutter: `code.toUpperCase()` before sending
- [ ] Flutter UI: `TextCapitalization.characters` + `LengthLimitingTextInputFormatter(6)`
- [ ] Logic driven by `CODE`, not `ID`

---

## /db-design — Full Pattern


# Database Design Rules

## General Rules

- **No FK constraints** — plain `INT` or `VARCHAR(36)` reference columns (keeps schema flexible across dev/qa/prod)
- **No column duplication** — don't repeat data derivable via JOIN. Exception: store `total_amount` on finance rows for audit trail
- **Separate booking_date from usage dates** — always store both administrative record date and operational usage window (`check_in_date`, `meal_date`, etc.)
- Financial amounts: `DECIMAL(10,2)` — never `INT`
- Drop `master_` prefix in FK column names: `master_astragen_apps.id` -> column `astragen_app_id`
- **Attendance by entity type:** separate tables per type. Employee: `emp_id INT`. External staff: `astragen_app_id INT + staff_id INT`
- **Reuse group + sub-category tables:** use `school_visit_groups.id` (not new event_schools/event_employees), use `ticket_sub_categories.id` (not new type master). Plain INT, no FK

## Standard Columns (every table — NO exceptions)

Every new `CREATE TABLE` MUST include the **audit quartet** and a **status column**. Omitting any of these is a hard FAIL caught by `guard-ddl-audit-columns.ps1` at `/done`.

| Column | Type | Default | Purpose |
|---|---|---|---|
| `status` | `VARCHAR(10) NOT NULL` | `'ACTIVE'` | Soft-delete / lifecycle; filter with `AND status = 'ACTIVE'` |
| `created_by` | `INT NOT NULL` | — | Employee DB ID who created the row |
| `created_on` | `DATETIME NOT NULL` | `CURRENT_TIMESTAMP` | Row creation timestamp |
| `updated_by` | `INT` | `NULL` | Employee DB ID of last updater |
| `updated_on` | `DATETIME` | `NULL ON UPDATE CURRENT_TIMESTAMP` | Auto-updated on row change |

**Column ordering convention:**
1. `id` (PK, auto-increment) — first
2. `uuid` (`VARCHAR(36) NOT NULL`) — second
3. Business columns
4. `status` — last business column
5. `created_by`, `created_on`, `updated_by`, `updated_on` — always the final four, in this order

**Phone columns:** `VARCHAR(20)` minimum — never `VARCHAR(15)` (Indian mobile 10 digits + country code + formatting space). See Contact Field Sizes below for the full table.

**Canonical DDL tail (copy-paste into every CREATE TABLE):**
```sql
  status VARCHAR(10) NOT NULL DEFAULT 'ACTIVE',
  created_by INT NOT NULL,
  created_on DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_by INT NULL,
  updated_on DATETIME NULL ON UPDATE CURRENT_TIMESTAMP
```

## Time Field Types

| Type | Use when |
|---|---|
| `TIME` | Recurring daily policy times (hotel check-in/check-out policy) |
| `DATETIME` | One-off timestamps tied to a specific calendar date |
| `DATE` | Time not relevant or derived from a policy table via JOIN |

## Contact Field Sizes (India standard)

| Column | Type | Purpose |
|---|---|---|
| `contact_person_name` | `VARCHAR(50)` | Sufficient for Indian names |
| `contact_person_phone` | `VARCHAR(20)` | 10-digit Indian number + formatting space |
| `contact_person_name_alt` | `VARCHAR(50)` | Secondary contact name |
| `contact_person_phone_alt` | `VARCHAR(200)` | Free-form; multiple numbers, names, or notes |

## Local Scripting Constraints

- Local Node scripts: NEVER `CREATE`/`ALTER`/`TRUNCATE`/`DELETE`/`UPDATE`/`INSERT`/`EXECUTE`/`DROP`
- Only `SELECT`/`DESCRIBE`/`SHOW COLUMNS` locally
- **`DESCRIBE` before any SQL (MANDATORY):** verify exact column names before writing any SQL

## Feedback Loop (mandatory)

1. Design the schema following the rules above
2. Run `DESCRIBE` on related tables to verify column names and types
3. Write DDL with correct field types and no FK constraints
4. Verify reference columns use plain `INT` or `VARCHAR(36)`
5. Only then check off the verification checklist items

## Verification Checklist

- [ ] **Audit quartet present:** `created_by`, `created_on`, `updated_by`, `updated_on` — all four, in this order, as the last four columns
- [ ] **`status` column present:** `VARCHAR(10) NOT NULL DEFAULT 'ACTIVE'`
- [ ] **Phone columns use `VARCHAR(20)`** — never `VARCHAR(15)` or smaller
- [ ] No `FOREIGN KEY` constraints — plain reference columns
- [ ] Financial amounts use `DECIMAL(10,2)`
- [ ] Booking date separate from usage dates
- [ ] FK column names drop `master_` prefix
- [ ] Time fields use correct type (TIME vs DATETIME vs DATE)
- [ ] Contact fields follow India-standard sizes
- [ ] Existing group/sub-category tables reused (not new ones created)
- [ ] `DESCRIBE` run on related tables before writing SQL

---

## /e2e-verification — Full Pattern


# End-to-End Data Verification Workflow

Automate all 4 stages for every module. Do NOT mark a feature done without completing all stages and producing the Stage 4 report.

**Entry point:** each sub-app has its OWN `lib/main_dev.dart` with a `_buildTestTarget()` line.
Use the dept's own file (e.g. `dashboard_admin/lib/main_dev.dart` to test an admin module) —
NOT `dashboard_app`'s. Full guide: `<sub-app>/lib/DEV_TESTING.md` where present.

## Stage 1 — Backend

- [ ] `node test_runner.js <handler> <METHOD> '<json>'` (run from `astragen_core_dashboard_<dept>/`)
- [ ] Returns `code: 200` with correct shape before Flutter work begins
- [ ] Record exact keys + values for Stage 3 comparison

## Stage 2a — Point main_dev at the module under test (BEFORE launching)

Launching without this risks verifying the wrong page — `_buildTestTarget()` may still point at
whatever was last tested. Always confirm the target first.

- [ ] Open the correct sub-app's `lib/main_dev.dart`
- [ ] **Read** the current `_buildTestTarget()` line — see what page it returns now
- [ ] If it isn't the module under test, edit that ONE line to return the target page
      (e.g. `=> const AdminWorkFlows(pageName: 'Master Data');`)
- [ ] Register any providers the page needs in the same file's `MultiProvider` — cross-ref
      `/provider-registration`. A missing provider throws at runtime, not compile time.
- [ ] **NEVER commit this swap** — it's a local test harness change only
- [ ] Hot-restart (`R`) if already running, else proceed to Stage 2 launch

## Stage 2 — Flutter UI (Claude in Chrome + dart MCP)

**Launch rule — use `web-server`, NOT `-d chrome`.** `flutter run -d chrome` spawns its *own*
Chromium that the Claude-in-Chrome extension cannot see or drive (verified: the extension only
finds a blank "New Tab"). Instead serve on a stable port and point the extension's Chrome at it:

```bash
# from <sub-app> root — profile mode keeps cold start fast (see /flutter-web-startup-perf)
flutter run -d web-server --profile --web-port 8080 -t lib/main_dev.dart
```
Then `mcp__Claude_in_Chrome__navigate` to `http://localhost:8080` in the extension browser.

**Channel reliability on Flutter web (CanvasKit) — measured, not assumed:**

| Channel | Tool | Use for |
|---|---|---|
| ✅ Network | `mcp__Claude_in_Chrome__read_network_requests` | **Data verification — the spine.** Real API calls + status codes |
| ✅ Visual | `mcp__Claude_in_Chrome__computer` (screenshot, `save_to_disk:true`) | Confirm rendered values, layout, real data on screen |
| ✅ Widget tree / errors | `mcp__dart__widget_inspector`, `mcp__dart__get_runtime_errors` | Flutter-native internals + Dart exceptions the browser can't see |
| ❌ DOM/semantic text | `get_page_text` / `read_page` | **Avoid.** CanvasKit renders to `<canvas>` — these return only bootstrap placeholders ("Starting") even after clicking "Enable accessibility". Do NOT depend on them. |

- [ ] Launch on `web-server --web-port 8080`, navigate extension Chrome to `localhost:8080`
- [ ] **Arm network first:** call `read_network_requests` once (tracking starts on first call),
      then reload — boot API calls fire before tracking otherwise and are missed
- [ ] `read_network_requests` (urlPattern e.g. `astragen.app`) — capture request + status
- [ ] `computer` screenshot (`save_to_disk:true`) — verify displayed values visually
- [ ] `mcp__dart__get_runtime_errors` — check for Dart exceptions

## Stage 3 — Data Comparison

| Check | Pass condition |
|---|---|
| Response keys | Flutter `fromJson` uses exact UPPER_SNAKE_CASE keys from backend |
| Data values | Screenshot shows values matching test_runner response (not page text — canvas) |
| HTTP status | Network 200 — no 4xx/5xx |
| List counts | UI row count = API result count |
| Pagination | Load-more advances offset correctly — no reset bug |
| Write -> Read | After save/update, UI reflects new data without full reload |

## Stage 4 — Report

```
## Data Verification Report — <Module> — <Date>
Backend: handler/METHOD -> 200   Sample: { KEY: "val" }
Flutter: network 200   Displayed: KEY = "val"
Result: PASS / FAIL  Issues: (list mismatches)
```

FAIL -> fix root cause, re-run all stages, re-report. Don't proceed until PASS.

## Feedback Loop (mandatory)

1. Run Stage 1 — confirm backend returns 200 with correct shape
2. Run Stage 2 — launch app, navigate, intercept network, read text
3. Run Stage 3 — compare every field: keys, values, counts, pagination
4. Write Stage 4 report
5. If FAIL: fix root cause, re-run ALL stages from Stage 1
6. Only PASS allows marking the feature as done

## Verification Checklist

- [ ] Stage 1: Backend returns 200 with correct response shape
- [ ] Stage 2: Flutter app loads, page renders, network request succeeds
- [ ] Stage 3: All keys match (UPPER_SNAKE_CASE), values match, counts match
- [ ] Stage 3: Pagination tested (offset advances, no reset)
- [ ] Stage 3: Write-then-read verified (save -> UI updates without reload)
- [ ] Stage 4: Report written with all fields documented
- [ ] Result: PASS — no mismatches

---

## /ui-test-data — Full Pattern


# UI-Test Data Seeding

One declarative scenario file per module captures the full dependency chain
(school → year → … → the rows the page shows) ONCE. After that, getting a page
testable is a single idempotent command. Canonical engine:
`vs-dashboard/astragen_core_dashboard_app/test_data/` (copy the folder to a
sibling backend on first use there; adjust only `config.js`).

## Core principles (locked)

1. **Seed through real handlers, not SQL.** The engine calls
   `handlers/<x>_handler.js` methods exactly like `test_runner.js` — dual
   writes (e.g. emp_claims two-table), denormalized columns, and side-effect
   rows are produced by production code, so seeds can't drift from the schema.
   Raw SQL is a fallback ONLY for tables with no write API (pre-aggregated
   report tables — `comprehensive_seed.js` precedent).
2. **Persistent tagged baseline, not seed→revert.** `seed.js` is
   ensure-exists + ensure-state; existing tagged rows are reused. Teardown is
   occasional cleanup, never a per-run step.
3. **Everything tagged**: `[UITEST]` inside description/text columns, `ZT`
   prefix in `code` columns (VARCHAR(6) rule). `teardown.js` re-verifies the
   live marker before each single-row DELETE; mismatch = skip, never delete.
4. **Dev-only hard guard**: both scripts exit unless every host in
   `config.guardHostKeys` contains `dev`. This is the sanctioned EXCEPTION to
   the "local scripts never write" rule — nothing else is.
5. **Anchor to the auto-login identity.** Seeds must attach to the test
   school / employee that the sub-app's `main_dev.dart` auto-login user can
   see, else the UI session finds an empty page anyway. Verify anchor ids via
   SELECT on dev before writing them into a scenario.
6. **People come from the persona registry** —
   `astragen_core_dashboard_app/test_data/personas.json` (workspace-wide
   canonical; sibling backends point to it via `config.personasPath`).
   Per department: one **head-of-dept** persona (Add/Edit access so
   manager-gated UI is testable) + **subordinate(s)** (reporting to the HoD,
   same dept group, View access). Adopt an existing dev user when one fits,
   create via the proper admin/HR flow when none does — filled in AS each
   department's first scenario lands. Scenarios reference them as
   `{personas.<dept>.<path>}` placeholders, never hardcoded emp ids. All dev
   users share the standard dev password (`_kDevPassword` in any sub-app's
   `main_dev.dart`) — never stored in the registry. Live/QA issues are
   reproduced by writing a scenario that approximates the reported shape —
   never by relying on live data or screenshots.

## Commands (from the backend root)

```bash
node test_data/seed.js <module>              # ensure data + states (idempotent)
node test_data/seed.js <module> --dry-run    # read-only preview
node test_data/seed.js <module> --validate   # lint scenario, no DB
node test_data/seed.js <module> --reset      # force re-apply states
node test_data/teardown.js <module> [--yes]  # plan / delete tagged rows
node test_data/teardown.js --all --yes       # spring-clean all scenarios
```

## Writing a scenario (`test_data/scenarios/<module>.scenario.json`)

Start from `_TEMPLATE.scenario.json`. Per entity, in dependency order:

| Field | Rule |
|---|---|
| `key` | unique; later entities reference it as `{entities.<key>.id}` |
| `table` + `tag` | where the row lives + which column carries the marker |
| `existsQuery/Values` | SELECT returning the id if already seeded (idempotence) |
| `create` | real `handler` + `method` + `body`; `idFrom` dot-path into response, else the engine re-runs existsQuery |
| `state` (opt) | desired status + the REAL status-change endpoint to reach it — never UPDATE SQL, that's where dual writes live |
| `teardownExtra` (opt) | DELETE-with-WHERE for side-effect rows (child rows first) |

Body rules are the standard API contract: engine auto-`.toString()`s numbers,
bools are `'TRUE'/'FALSE'/'NA'`, lists pass `[]`, sentinels `'NA'/'-1'/'-1.0'`
(`/api-body-pattern`). Use endpoints that DON'T touch the old DB
(`EXECUTE_QUERY_IN_ASTRAGEN_OLD_DB`) — its host is outside the guard.

## Workflow integration

- **/done step 10**: the ui_verify test file starts with a
  `## Step 0 — Data prerequisites` section naming the scenario + seed command.
  If no scenario exists for the touched module, WRITE one (or extend it) as
  part of /done — that is the moment the dependency knowledge is fresh.
- **The user runs the seed command themselves**, then drives UI verification
  on their own Sonnet session (per `feedback_no_ui_verify_runner_spawn` —
  Claude never spawns the runner or the app).
- New scenario SQL identifiers follow `feedback_verify_sql_identifiers_against_dev`:
  grep the canonical join in the owning backend, and `--dry-run` once against
  dev (read-only) before declaring the scenario done.

---

## /m3-ui-replicator — Full Pattern


# M3 UI Replicator Skill

Recreates reference UI screens (HTML/JS, Dart, or image) as production-grade Flutter/Dart files
using Material 3 principles, the project's existing theme, and established project conventions.

---

## Core Mandate

| What to PRESERVE from reference | What to IGNORE from reference | What to ENFORCE |
|---|---|---|
| Layout structure & composition | Colors & color palette | Project's `ThemeData` / `ColorScheme` |
| Navigation patterns | Typography choices | M3 component equivalents |
| Screen flow & hierarchy | Shadows / elevation style | Project's Provider state pattern |
| Data fields & form structure | Platform-specific widgets | Project file/folder conventions |
| Component placement & density | Non-M3 custom widgets | Flutter latest stable APIs |
| User interaction patterns | Hardcoded dimensions | Responsive/adaptive sizing |

**Rule**: The reference is a layout blueprint. The project's theme is the visual truth.

---

## Step 1 — Understand the Module Discussion First

Before touching the reference file, fully parse the user's module description:

- What is the **purpose** of this module? (CRUD, reporting, workflow, auth, etc.)
- What **entities/data** does it handle?
- What **screens/routes** are involved?
- What **actions** does the user take? (create, view, edit, approve, navigate)
- Are there **functional deviations** from the reference the user explicitly called out?

Note deviations explicitly — these are screens or behaviors that must **differ** from the reference.

---

## Step 2 — Deep Reference Analysis Protocol

### For HTML/JS files:

Parse in this order:

1. **Page/route inventory** — how many distinct screens or views exist?
2. **Layout skeleton** — identify the top-level structure:
   - AppBar / TopBar presence and content (title, actions, back button)
   - Bottom navigation or drawer?
   - Body layout: list, grid, form, detail, dashboard?
   - FAB or primary action button?
3. **Component inventory per screen** — list every interactive or display element:
   - Input fields (text, dropdown, date, checkbox, radio, toggle)
   - Buttons (primary, secondary, icon)
   - Cards, tiles, list items
   - Dialogs, bottom sheets, snackbars
   - Tables, data grids
4. **Data fields** — extract all labels, placeholder text, field names (these carry over)
5. **Navigation flow** — how do screens connect? (tabs, push, replace, modal)
6. **State indicators** — loading states, empty states, error states visible in the reference

### For Dart files (single or multi-file project):

1. Read `pubspec.yaml` first — note dependencies, especially state management and UI packages
2. Identify the widget tree structure per screen
3. Extract all `Text()` labels, field names, route names
4. Note any custom widgets — these need M3 equivalents
5. Identify navigation: `Navigator.push`, `GoRouter`, named routes
6. Extract data models or classes referenced

### For screenshots / images:

1. Describe the layout grid (columns, rows, cards)
2. Identify every visible UI element by function, not appearance
3. Note placement: top/center/bottom, left/right/full-width
4. Count list items, tabs, form fields visible
5. Identify navigation chrome (AppBar, BottomNav, Drawer handles)

---

## Step 3 — Read the Project Theme

**ALWAYS do this before generating any widget code.**

Look for the project theme in this priority order:

```
lib/core/theme/
lib/theme/
lib/config/theme/
lib/app_theme.dart
lib/theme.dart
```

Extract and note:
- `ColorScheme` — primary, secondary, tertiary, surface, background, error and their `on*` variants
- `TextTheme` — display, headline, title, body, label styles and font family
- `ThemeData` extensions if any (custom colors, component themes)
- `AppBarTheme`, `CardTheme`, `InputDecorationTheme`, `ElevatedButtonTheme`, etc.

If the theme file is not shared, **ask the user** before proceeding:
> "I don't see your project's theme file in the shared context. Can you paste `app_theme.dart` or equivalent so I use your exact ColorScheme and TextTheme?"

---

## Step 4 — Read Project Conventions

Check for existing project rules in this order (these are non-negotiable):

```
.claude/rules.md
.claude/memory.md
CLAUDE.md
README.md (architecture section)
```

Extract:
- State management pattern (confirmed: Provider — never deviate)
- Folder structure for new screens
- Naming conventions (snake_case files, PascalCase widgets)
- Import style (relative vs package imports)
- Any established base widgets (e.g., `AppScaffold`, `PrimaryButton`)

If the user hasn't specified output file location, **ask once**:
> "Where should I create these files? e.g., `lib/features/[module_name]/screens/`"

---

## Step 5 — M3 Component Mapping

Map every reference component to its M3 Flutter equivalent:

| Reference Pattern | M3 Flutter Equivalent |
|---|---|
| Any primary action button | `FilledButton` |
| Secondary / cancel button | `OutlinedButton` or `TextButton` |
| Raised/elevated button | `ElevatedButton` (M3 tonal) |
| Custom card container | `Card` with `elevation` and `shape: RoundedRectangleBorder` |
| Input field (any style) | `TextField` with `InputDecoration` using `OutlineInputBorder` or `filled: true` |
| Dropdown / select | `DropdownMenu` (M3) — NOT deprecated `DropdownButton` |
| Checkbox list item | `CheckboxListTile` |
| Switch / toggle | `Switch.adaptive` or `SwitchListTile` |
| App header / navbar | `AppBar` with `scrolledUnderElevation` |
| Bottom navigation | `NavigationBar` (M3) — NOT `BottomNavigationBar` |
| Side drawer | `NavigationDrawer` (M3) |
| Floating action | `FloatingActionButton` (standard, small, large, extended as needed) |
| Search bar | `SearchBar` or `SearchAnchor` (M3) |
| Chips / tags | `FilterChip`, `InputChip`, `ActionChip` |
| Progress/loading | `CircularProgressIndicator.adaptive` or `LinearProgressIndicator` |
| Divider | `Divider` with M3 default thickness |
| Bottom sheet | `showModalBottomSheet` with `useSafeArea: true` |
| Dialog | `showDialog` with `AlertDialog` (M3 style) |
| Snackbar | `ScaffoldMessenger.of(context).showSnackBar` |
| List tile | `ListTile` with proper `leading`, `trailing`, `subtitle` |
| Tab bar | `TabBar` with `TabBarView` inside `DefaultTabController` |

---

## Step 6 — Screen Generation Protocol

Generate each screen in this structure:

```dart
// [screen_name]_screen.dart
// Module: [module name]
// Reference: [what reference file this was based on]
// M3 components used: [list key ones]

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
// ... other imports following project convention

class [ScreenName]Screen extends StatelessWidget {
  // or StatefulWidget only if local UI state needed (form controllers, etc.)
  
  const [ScreenName]Screen({super.key});

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context); // ALWAYS use this — never hardcode colors
    final colorScheme = theme.colorScheme;
    final textTheme = theme.textTheme;
    
    // Provider consumption here
    
    return Scaffold(
      // M3 scaffold — no backgroundColor override unless explicitly needed
    );
  }
}
```

**Color rule**: Every color reference in generated code must use `colorScheme.*` or `theme.*`. Zero hardcoded hex values. Zero `Colors.*` constants unless it's `Colors.transparent`.

**Sizing rule**: Use `MediaQuery.sizeOf(context)` for responsive dimensions. Avoid hardcoded pixel values for layout — use `EdgeInsets`, `SizedBox` with relative values, or `Expanded`/`Flexible`.

---

## Step 7 — Output Checklist (run before finalizing)

Before presenting generated files, verify:

- [ ] Every color comes from `theme.colorScheme` or `theme.textTheme`
- [ ] No M2 deprecated widgets (`BottomNavigationBar`, `DropdownButton`, `RaisedButton`)
- [ ] All navigation patterns match existing project router setup
- [ ] Provider consumers are correctly scoped (`Consumer<T>`, `context.watch<T>()`, `context.read<T>()`)
- [ ] All text labels from the reference are preserved (field names, titles, action labels)
- [ ] Layout structure matches the reference — not just visually but structurally (same number of sections, same hierarchy)
- [ ] Empty state, loading state handled for any list or async data screen
- [ ] `const` constructors used wherever possible
- [ ] No hardcoded strings — use `const` strings or point to where l10n keys go

---

## Step 8 — Deviation Report

After generating screens, always output a short **Deviation Report**:

```
## Deviation Report — [Module Name]

### Preserved from reference:
- [layout element 1]
- [navigation pattern]
- [form structure]

### Upgraded to M3:
- [Old component] → [M3 equivalent] (reason)

### Intentionally changed (per discussion):
- [element] changed to [new behavior] because [user stated reason]

### Assumptions made:
- [anything not explicit in reference or discussion — flag these for user confirmation]
```

---

## Reference-Type Quick Routing

```
User shares...           → Go to...
─────────────────────────────────────────────────
.html / .js file         → Step 2 (HTML/JS analysis)
.dart file(s)            → Step 2 (Dart analysis)  
Screenshot / image       → Step 2 (image analysis)
Mix of file + screenshot → Analyze both; screenshot resolves ambiguity
Text description only    → Skip Step 2; go straight to Step 3
```

---

## What This Skill Does NOT Do

- Does not invent functionality not present in the reference or module discussion
- Does not change the number of screens without explicit discussion
- Does not introduce new state management patterns (Provider is fixed)
- Does not generate BLoC, Riverpod, GetX, or any non-Provider state code
- Does not override the project's theme with its own color/font choices
- Does not produce HTML, web, or non-Flutter output
- Does not guess the output file path — asks if not clear from context
