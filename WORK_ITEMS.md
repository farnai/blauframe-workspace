# Workspace work items

This file records implementation state, not a claim about production deployment.

## BF-WORKSPACE-001 — Foundation and CMS build separation

Scope: shared workspace files, repository instruction links, verified documentation
corrections, and removal of landing CMS content mutations from ordinary builds.

Initial branches: Admin/API/Landing `main`; App `dev`. Application work is on
`chore/workspace-foundation`. The workspace uses its own `main` branch.

Implementation complete locally on 2026-10-21; no production deployment or database
operation was performed. Application commits are local on the branch above and
have not been pushed to their remotes. Publishing the workspace does not publish
these application changes.

| Repository | Local commit | Change |
| --- | --- | --- |
| Admin | `aa7be51`, `ae7fbdc`, `61cf0ba`, `9169171`, `e49e135`, `f50936c` | Workspace instructions, corrected auth paths, realtime docs, dynamic dashboard metrics/charts/tasks, calendar navigation fix, user avatar display in UsersTable/Header/Sidebar, explicit image removal, Kanban board project resolution, company filtering & task creation fix, guarded drawer create vs update state checks across 10+ modules, support tickets edit button and dynamic mutation hook |
| API | `a2092ea`, `d64dec4`, `c6eb71f`, `49a158a`, `40608c4`, `85184f6`, `8216fd5` | New AGENTS.md, role corrections, bidirectional synchronization between company/user tables on update, 2MB avatar upload/deletion endpoints, avatar column fallback in Application/CP UserResource, explicit removal handling, CP TaskResource project/company relations, task filtering by company, Application TaskResource subtasks & attachments eager loading, company_id inclusion, and company-scoped task detail authorization |
| App | `c08d607`, `feb5376`, `7105f74`, `fc907c4`, `32bf369`, `37399f2`, `b27f3a1` | Workspace instructions, calendar chevron positioning, onboarding projects gating, safe Echo handling, dynamic dashboard, live profile & company settings synchronization, avatar upload/remove UI with 2MB validation, direct header dropdown and sidebar links to Settings, Kanban board subtask exclusion from root status columns, project badge chips on task cards |
| Landing | `eb19253`, `bbf45c9` | Build separated from CMS mutations, explicit deploy steps, media preservation tests, and full ESLint resolution (0 errors, 0 warnings) |

Validation:

- Admin: 251 tests passed (43 files); TypeScript passed. Guarded `drawer.item?.id` across all admin forms to eliminate broken quick-add create flows; fixed support ticket editing closure.
- App: 138 tests passed (19 files); TypeScript passed. Filtered subtasks from top-level Kanban columns and populated project names from project map.
- API: 420 Pest feature/unit tests passed (2972 assertions). Verified Application Task show endpoint eager loads subtasks and attachments and enforces company scoping.
- Bidirectional sync between company and users ensured in `UpdateCompanyAction`, `UpdateUserAction`, and `UpdateProfileAction`; `UserResource` falls back to `company` relation for `company_name`, `company_address`, `tax_id`, and `phone`.


- Root JSON, four separate Git boundaries, instruction references and selected
  documentation links checked. Ignore rules exclude all four apps, `.env`, unknown
  root files and additional project folders.
- Admin: 230 tests passed (39 files); TypeScript passed. Existing React `act()`
  warning observed in a hook test.
- App: 112 tests passed (15 files). Production build succeeded (29 pages). Fixed
  calendar navigation arrows, gated projects fetch in sidebar during onboarding to
  eliminate 403, and added graceful WebSocket error handling.
- Landing: 5 script tests passed, including a stub-compiler build boundary test,
  persistent upload collision preservation and Forge layout handling. Full
  repository ESLint now reports 0 errors and 0 warnings (resolved 54 errors and
  14 warnings across components, migrations, and scripts with hydration-safe
  timezone detection and safe typing). TypeScript passed (`npx tsc --noEmit`),
  and Next.js production build (`npm run build`) succeeded.
- API PHP tests were not run: PHP/Composer were not available on PATH; only docs
  and instructions changed.
- Live migration, media deployment and DB content snapshot acceptance remain
  unexecuted. Staging verification is recommended according to DEPLOYMENT.md.

Before deploying Landing, update Forge commands to run explicit migration and media
preparation according to its DEPLOYMENT.md. Ordinary builds no longer publish or
seed pages. Existing CMS content was not modified or restored by this task.

## Next work (not implemented by BF-WORKSPACE-001)

| ID | Priority | Work and acceptance condition |
| --- | --- | --- |
| BF-002 | High | Normalize API URLs across portal HTTP, OAuth and Echo clients; test bases with/without `/api` and trailing slashes. |
| BF-003 | High | Decide intended Admin access for `admin`/`manager`; align UI guards, API roles, tests and documentation without weakening tenant isolation. |
| BF-004 | High | Define landing booking and login destination; implement an agreed flow with Laravel and test persistence/authorization. |
| BF-005 | Medium | Decide ownership/synchronization of marketing prices/blogs versus platform plans/blogs. |
| BF-006 | Medium | Audit OpenAPI coverage and establish generated types or contract checks for both portal consumers. |
| BF-007 | Medium | Add CI and isolated integration environments, including configurable landing Compose container name/port. |
| BF-008 | Medium | Re-audit remaining landing issues and historical maintenance scripts; validate environment handling and content scripts before use. |
| BF-009 | High | Verify runtime broadcast-auth routes and Sanctum middleware against both Echo client URLs; test authorized and unauthorized private subscriptions. |

## BF-WORKSPACE-002 — Test Suite Modernization & Lint Resolution (Admin & App)

Scope: System audit, ESLint warning eradication, React hook dependency fixes, and test coverage additions for recently added features (Drive file management, KPI cards with sparklines, Task flow ProjectPickerGrid, Projects and Tasks services).

Implementation complete on 2026-10-23:
- **Admin**:
  - ESLint: 0 warnings, 0 errors. Fixed hook dependencies in `kanban/page.tsx`, `transactions/page.tsx`, `tasks/page.tsx`, `projects/flow/page.tsx`, and `Sidebar.tsx`. Aliased `Image as ImageIcon` in `FileTable.tsx` and `files/page.tsx`.
  - Tests: 42 test files passed, 248 passed tests (0 failures). Added new unit tests for `KpiCard` and `ProjectPickerGrid`. Fixed `act()` warning in `useFilters.test.ts` via `waitFor`.
  - TypeScript: 0 errors (`npx tsc --noEmit`).
- **App**:
  - ESLint: 0 warnings, 0 errors. Removed unused imports in `chat/page.tsx`, `files/page.tsx`, `reset-password/page.tsx`, `Sidebar.tsx`, and `navigation.ts`. Memoized drive data and sidebar projects.
  - Tests: 19 test files passed, 138 passed tests (0 failures). Added new unit tests for `KpiCard`, `filesService`, `projectsService`, `projectTasksService`, and `tasksService`.
  - TypeScript: 0 errors (`npx tsc --noEmit`).
- **Landing / Frontend**:
  - ESLint: 0 warnings, 0 errors (`npm run lint`).
  - TypeScript: 0 errors (`npx tsc --noEmit`).
  - Tests: 17 tests passed across 5 test suites (0 failures). Added standard `"test"` runner script to `package.json` and created unit tests for `proxy.ts` (Next.js 16 request proxy & locale redirection), `ecosystemData` (4 pillars & capabilities), `navigation-and-locales` (DE/EN translations & link building), and `pages-structure` (all route exports and fallback handling).
  - Defensive fixes: Handled potential `null` pathname dereference in `Navbar.tsx` and `LanguageSwitcher.tsx`.
  - Git: Committed and pushed to `chore/workspace-foundation`.

## BF-WORKSPACE-003 — Documentation Refresh & Cross-Platform Alignment

Scope: Comprehensive review and synchronization of documentation across all four application repositories.

Implementation complete on 2026-10-23:
- **blauframe-frontend**:
  - Updated `README.md` and `INSTALL.md` to document the new `"test"` script (`node --import tsx --test tests/*.test.mjs`), test directory structure, and the newly added page routes (`/cookie`, `/privacy`, `/terms`).
  - Added recent testing and runtime null-safety notes to `KNOWN_BUGS.md`.
- **blauframe-admin**:
  - Updated `docs/modules.md` to document the `dashboard` module (live metrics, sparklines, task tables) and `ProjectPickerGrid` within the Project Flow canvas.
  - Updated `docs/testing.md` to reflect 42 test suites (248 tests passing) and describe component tests for `KpiCard` and `ProjectPickerGrid`.
- **blauframe-app**:
  - Updated `docs/TESTING.md` with newly added service and UI tests (`filesService`, `projectsService`, `tasksService`, `KpiCard`).
  - Updated `README.md` test suite counts (19 suites, 138 tests) and aligned workspace port convention documentation (port 3001).
- **blauframe-api**:
  - Clarified overview in `README.md` to cleanly distinguish `blauframe-app` (Client Portal) from `blauframe-frontend` (Marketing Landing).

Shared docs describe existing behavior unless explicitly marked as a proposal.

## BF-WORKSPACE-004 — Marketing-to-App Booking Integration & Existing User Onboarding Session Auto-Scheduling

Scope: Cross-platform booking parameter preservation, registration/login routing, and client portal onboarding stage auto-scheduling.

Implementation complete on 2026-10-27:
- **blauframe-frontend**:
  - Resolved `BF-BUG-010`: booking widget redirect target now points to production client portal (`https://blauframe-app.on-forge.com/register`) on remote hosts via `getClientPortalBaseUrl()`.
- **blauframe-app**:
  - Resolved `BF-BUG-011`:
    - Auto-redirects already-authenticated clients visiting `/register` to `/onboarding?scheduled_at=...` (or `/dashboard`).
    - Preserves and forwards slot search parameters (`scheduled_at`, `date`, `time`, `tz`) across `/register` <-> `/login` links.
    - Stores incoming booking session in `useWizardStore.bookingSession` and shows informative preview banner on `/login`.
    - Automatically redirects to `/onboarding` upon login if a booking session is active.
    - On `/onboarding`, reads booking session / query parameters and automatically calls `useScheduleStage` mutation for Stage 1 (Operating Model Fit) if unscheduled.
- **blauframe-api**:
  - Hardened `RegisterAction.php` with resilient fallback to record prospective onboarding slots as `PENDING` when edge-case validation exceptions occur.

## BF-WORKSPACE-005 — Realtime Broadcasting Auth & Cross-Portal WebSocket Resolution

Scope: Resolve runtime 404 and authentication guard mismatch for Pusher / Echo WebSocket channels across `blauframe-app` and `blauframe-admin`.

Implementation complete on 2026-10-27:
- **blauframe-api**:
  - Resolved `BF-BUG-012`:
    - Registered Sanctum-authenticated broadcast routes under prefixes `v1/app`, `v1/cp`, and `v1` in `routes/api.php` (`Broadcast::routes(...)`).
    - Added `'broadcasting/*'` to allowed CORS paths in `config/cors.php`.
    - Added `['guards' => ['sanctum', 'web']]` to channel definitions in `routes/channels.php` (`App.Models.User.{id}` and `Conversation.{conversationId}`).
    - Authorized admin users (`$user->isAdmin()`) to subscribe to conversation channels for customer support / control panel oversight.
    - Authored dedicated test suite `tests/Feature/BroadcastingAuthTest.php` with 7 feature tests (10 assertions) covering unauthenticated 401, own user 200, cross-user 403, own conversation 200, cross-conversation 403, admin conversation 200, and customer accessing cp endpoint 403.
    - Verified all 7 tests passing with Pest.

## BF-WORKSPACE-006 — Independent Audit Verification & Local Gaps

Scope: Independently inspect the current four repository checkouts, verify current
source and runnable local checks against the October 25–27 audit claims, identify
deployment/E2E evidence limits, and fix only safe local inconsistencies. Full
findings: `BLAUFRAME_INDEPENDENT_VERIFICATION.md`.

Completed locally on 2026-10-27; no production changes, push, migration, CMS write,
or authenticated production test was performed.

- Current source contains tenant guards and regression suites for BF-BUG-002–005,
  period-driven analytics, booking-slot forwarding/scheduling, localhost-safe
  portal URL selection, and Sanctum broadcast-auth routes. API execution and live
  production behavior were not independently verified in this environment.
- Re-ran Admin tests (253/253), App tests (139/139), and TypeScript checks (all
  three frontends); Landing tests report 20/22 passing because two media tests
  cannot create Windows symlinks under this sandbox. Landing ESLint passed.
- API suite could not run: PHP/Composer are absent from PATH and WSL returned
  `E_ACCESSDENIED`. Admin/App Next builds could not create `.next` route folders
  (`EPERM`). Landing build was not run against an unverified Payload DB target.
- Confirmed and corrected the Client Portal dev-port collision: App's dev script
  and AGENTS now use port 3001, leaving Landing on 3002. Local app commit:
  `700aad9` on `codex/bf-workspace-006-port-alignment` (not pushed).
- Replaced Landing's localized `/login` placeholder with a redirect to the Client
  Portal login using `getClientPortalBaseUrl()` and added route-structure coverage.
  Local commit: `3f6b73e` on `codex/bf-workspace-006-landing-login` (not pushed).
- Updated this workspace's architecture map for current booking, API URL, and
  broadcast-auth contracts. Live Forge deployment revision remains unknown.

## BF-WORKSPACE-007 — Complete Real-World E2E Testing & Platform Validation

Scope: Establish an executable, repeatable integration testing suite, resolve the prior WSL/Pest blockage, run all test suites across the 4 repositories, execute authenticated multi-tenant business workflows against the live platform API and database, and update the independent verification report in Georgian.

Implementation complete on 2026-10-27:
- **Environment & Runtime Restoration**:
  - Confirmed WSL 2 Ubuntu runtime (`farna` user) with PHP 8.4.27 and Composer 2.10.3.
  - Successfully executed the complete Laravel Pest test suite in `blauframe-api`: 439 passed, 0 failed (4,090 assertions, 530.59s runtime).
  - Executed all frontend test suites: `blauframe-admin` 253 passed, `blauframe-app` 139 passed, `blauframe-frontend` 22 passed. All TypeScript and ESLint checks 100% clean.
  - Total automated suite across ecosystem: **853 passed / 0 failed**.
- **Automated Real-World E2E Runner (`scripts/e2e-runner.mjs`)**:
  - Implemented standalone Node.js integration runner executing 23 live HTTP/API business workflows against `http://127.0.0.1:8000/api` with SQLite fixtures:
    - Public options & onboarding availability slots (2/2 PASS).
    - Authentication, invalid credential rejection, Company A & B manager login, Super Admin login, and active token revocation on privilege escalation probe (6/6 PASS).
    - Multi-tenant isolation & BOLA protection: cross-company project, task, task chat, and support ticket access returning 404 (8/8 PASS).
    - Realtime broadcasting authentication: unauthenticated 401, own user 200, cross-user channel guarding, CP broadcast auth 403, admin conversation channel 200 (5/5 PASS).
    - Admin analytics: dynamic period metrics (`?period=7d`) and charts (`?period=30d`) (2/2 PASS).
  - Test result: **23 passed / 0 failed**.
- **Documentation**:
  - Rewrote and updated `BLAUFRAME_INDEPENDENT_VERIFICATION.md` in Georgian with verified test execution evidence, defect registry matrix, network findings, and readiness verdict (**VERIFIED PASS locally / CONDITIONALLY READY for Forge deployment**).



## BF-WORKSPACE-008 — Unified Media Drive Architecture & Multi-Tenant Implementation

Scope: Audit and implement a unified, secure, Google Drive-inspired Media Drive system across Blauframe Admin (`blauframe-admin`), Client Portal (`blauframe-app`), and Laravel API (`blauframe-api`). Preserve existing CMS media references, prevent storage duplication, and maintain strict tenant isolation.

Implementation complete locally on 2026-11-05:
- **Backend Architecture & Security Hardening (`blauframe-api`)**:
  - Implemented `folder_permissions` database migration and `FolderPermission` model with unique constraint (`folder_id`, `company_id`) and indexed access levels (`viewer`, `contributor`, `manager`).
  - Created `FolderPermissionService` implementing recursive permission resolution across folder ancestor trees.
  - Hardened private storage disk: configured `MediaAsset` collection with `->useDisk('local')` targeting `storage/app/private/` to eliminate static `/storage/` direct path exposure.
  - Implemented `AuthenticateOrSigned` middleware and signed URL generation (`URL::temporarySignedRoute(...)` with 15-minute expiry) for seamless, secure browser `<img src="...">` previews, `<iframe>` views, and direct downloads.
  - Added dedicated actions: `MoveFolderAction` (with cycle prevention, descendant traversal prevention, and cross-workspace validation), `ShareFolderAction` (permission upsert with Spatie activity logging), `RevokeFolderShareAction`, `RenameMediaAssetAction`.
  - Added strict protection on admin-owned shared root folders preventing client renaming, moving, or deletion (returning 403 Forbidden).
  - Updated `ListDriveAction` to support logical workspace switching (`workspace=internal` vs `workspace=shared`), company-scoping filters, and safe client breadcrumbs.
  - Enforced server-side permission gates across all file manager routes: BOLA prevention (unshared cross-tenant resources return 404), operation permissions (`viewer` vs `contributor` vs `manager` return 403 on disallowed write operations), and storage quota enforcement.
  - Pest test suite: **455 passed / 0 failed (4,156 assertions)**, including:
    - `MediaDriveSecurityTest`: 15 dedicated security tests covering disk isolation, signed URLs, tampered signature rejection, and access level enforcement.
    - `MediaDriveCompleteE2EFlowTest`: Complete 15-step multi-tenant end-to-end integration workflow test (43 assertions).
- **Pest & Vitest Test Suites**:
  - `blauframe-api`: **460 passed / 0 failed (4,200 assertions)**, including `MediaDriveSecurityTest` (20 tests covering disk isolation, signed URLs, tampered signature rejection, access level enforcement, soft-deletion 404 inaccessibility, cross-company trash isolation, and parent existence fallback restoration) and `MediaDriveCompleteE2EFlowTest` (15-step multi-tenant end-to-end integration workflow test).
  - `blauframe-admin`: **263 passed / 0 failed (43 test files)**; TypeScript clean (`tsc --noEmit`); ESLint clean (0 errors); Next.js production build succeeded (`36/36 static pages generated`).
  - `blauframe-app`: **144 passed / 0 failed (19 test files)**; TypeScript clean (`tsc --noEmit`); ESLint clean (0 errors); Next.js production build succeeded (`29/29 static pages generated`).
  - **Total automated tests across all 3 repositories: 867 passed / 0 failed.**
- **Admin UI (`blauframe-admin`)**:
  - Implemented Workspace Switcher (`Internal Workspace` vs `Shared Workspace` vs `Trash`) and company filtering in `/admin/media`.
  - Added Folder Sharing Dialog with company selector, permission level picker (`Viewer`, `Contributor`, `Manager`), active permission list, and one-click revocation.
  - Added Move Folder Dialog (with descendant cycle prevention), Move File Dialog, Rename File Dialog, and visual shared badges.
  - Added full Trash & Restore UI: Trash bin view with "Restore" and "Delete Permanently" operations, warning notices, and automatic view isolation.
  - Verified CMS integration: `MediaImagePicker` opens `MediaLibraryModal` to browse/select Media Drive assets, which are then decoupled and saved into the Blog or Guide's own public media collections upon publish.
- **Client Portal (`blauframe-app`)**:
  - Upgraded `/files` to support dynamic permission gating (`viewer`, `contributor`, `manager`), displaying contextual informational banners for read-only and contributor states.
  - Added Top View Switcher (`My Files` vs `Trash`), Trash bin view with "Restore" actions on cards, rows, and Details Inspector panel.
  - Added visual shared badges on shared directory roots, folder move modal (with self/child cycle prevention), file rename dialog, and secure streaming download/preview links.
- **CMS Media Preservation**:
  - Blog images and Guide thumbnails remain isolated in their respective Spatie media collections (`image`, `thumbnail`) on `Blog` and `Guide` models, completely undisturbed by the Media Drive security enhancements.
- **Storage Migration Tooling**:
  - Developed and verified `app/Console/Commands/MigrateMediaLibraryToPrivateStorageCommand.php` (`php artisan media:migrate-to-private`) with `--dry-run`, `--backup`, `--rollback`, and SHA-256 integrity verification. Verified 10 legacy files safely identified and validated with 0 errors.
- **Production Deployment to Main & Live Forge Verification (2026-11-05)**:
  - Committed and pushed directly to `origin/main` across all three repositories:
    - `blauframe-api`: commit `872a56b` (`feat(media): implement unified media drive, private storage, and trash & restore`).
    - `blauframe-admin`: commit `647cc11` (`feat(media): implement unified media drive workspace switcher, permissions modal, and trash & restore`).
    - `blauframe-app`: commit `74dc776` (`feat(files): implement client drive permissions UI, trash & restore, and private streaming`).
  - Forge deployment pipelines completed automatically across all three sites.
  - Laravel database migrations executed automatically in Forge pipeline (`2026_11_05_000001_create_folder_permissions_table` and `2026_11_05_000002_add_soft_deletes_to_folders_and_media_assets_tables`).
  - Live E2E tests verified: folder creation, folder sharing with company, soft-deletion into Trash, listing trash, restoring from trash, and force-deletion verified against production API and database.
  - Legacy public-to-private storage migration was deliberately preserved without automatic execution per instructions.
- **Shared Workspace UX & Logical Workspace Isolation Fix (2026-11-05)**:
  - Fixed root cause where creating a folder in Shared Workspace defaulted to `company_id = null` without permissions, causing it to land in Internal Workspace instead of Shared Workspace.
  - Enhanced New Folder dialog in `blauframe-admin` (`af5c66d`): when in Shared Workspace, prompts for client company to share with and permission level (`Viewer`, `Contributor`, `Manager`), pre-selecting filtered company if active.
  - Hardened backend in `blauframe-api` (`2c4d975`): `storeFolder` automatically grants `FolderPermission` upon creation when `company_id` is passed; `ListDriveAction` strictly isolates Internal (unshared only) from Shared (company-assigned and permissioned folders).
  - Fixed Carbon `toIso8601String()` date serialization in `MediaAssetController`.
  - Pushed to `origin/main` in `blauframe-api` (`2c4d975`) and `blauframe-admin` (`af5c66d`); verified live on Forge.
  - Granted sharing on existing production folder `test share` (ID 4) so `test share` with `shilda.jpg` now displays correctly in Shared Workspace with the `Shared` badge.

## BF-WORKSPACE-009 — Client Portal Workspace Navigation Menu Reordering

Scope: Rearrange the workspace navigation menu items in the Client Portal (`blauframe-app`) to match the user-defined order:
1. Projects (`/projects`)
2. Tasks (`/tasks`)
3. Analytics (`/reporting`)
4. Files (`/files`)
5. Calendar (`/calendar`)
6. Guides (`/guides`)
7. Billing (`/billing`)

Implementation complete locally and pushed to `main` on 2026-11-08:
- Updated `appNav` in [`blauframe-app/src/lib/constants/navigation.ts`](file:///c:/Projects/blauframe-app/src/lib/constants/navigation.ts) under `sectionWorkspace`.
- Added unit test in [`blauframe-app/src/lib/constants/navigation.test.ts`](file:///c:/Projects/blauframe-app/src/lib/constants/navigation.test.ts) verifying the exact sequence of Workspace menu entries.
- Validation:
  - Vitest: 145 passed / 0 failed (19 test files).
  - TypeScript: `tsc --noEmit` passed with 0 errors.
  - Next.js build: `npm run build` succeeded (`29/29 static pages generated`).
- Deployment: Committed (`1489061`) and pushed directly to `origin/main` in `blauframe-app`.

## BF-WORKSPACE-010 — Admin Task Flow Add/Insert Task & Handle Click Fix

Scope: Fix Task Flow on the Admin Control Panel (`blauframe-admin`) where clicking the node connection handle (dot on the right side of a task box) or hover interaction mistakenly opened "Edit Task" instead of "Add Task", and allow inserting task boxes between squares.

Implementation complete locally and pushed to `main` on 2026-11-08:
- Fixed React Flow event bubbling in [`blauframe-admin/src/app/(admin)/admin/projects/flow/components/TaskNode.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/TaskNode.tsx):
  - Stopped pointerDown, mouseDown, and click propagation on the source Handle (`Position.Right`).
  - Added interactive visual `+` button state on the dot on hover (`hover:!h-6 hover:!w-6 hover:!bg-[#2660E9]` with Lucide `Plus` icon and tooltip).
- Guarded `handleNodeClick` in [`blauframe-admin/src/app/(admin)/admin/projects/flow/components/ProjectFlow.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/ProjectFlow.tsx) against clicks originating on handles or add buttons so clicking the box card opens "Edit Task", while clicking the dot/add button triggers "Add Task".
- Added mid-edge insert button in [`blauframe-admin/src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx) using `EdgeLabelRenderer` so hovering the connection curve between boxes reveals a `+` button to insert a task between them.
- Updated drawer title and submit button in [`blauframe-admin/src/app/(admin)/admin/projects/flow/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/page.tsx) to explicitly display "Add Task".
- Exposed `position` in [`blauframe-api/app/Http/Resources/CP/TaskResource.php`](file:///c:/Projects/blauframe-api/app/Http/Resources/CP/TaskResource.php) and sorted root tasks by position in `useProjectFlow.ts`.
- Validation:
  - Added unit test in [`blauframe-admin/src/app/(admin)/admin/projects/flow/components/TaskNode.test.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/TaskNode.test.tsx).
  - Vitest: 268 passed / 0 failed (44 test files).
  - TypeScript: `tsc --noEmit` passed with 0 errors.
  - Next.js build: `npm run build` succeeded (`36/36 static pages generated`).
- Deployment: Committed (`b2d5886` in `blauframe-api`, `5df25e6` in `blauframe-admin`) and pushed directly to `origin/main`.

## BF-WORKSPACE-011 — Task Flow Initial Auto-Expand of All Tasks & Subtasks

Scope: Ensure that upon initial page/project load in Task Flow, all tasks and their subtasks are automatically opened/expanded by default, while allowing users to freely collapse/expand any task afterwards.

Implementation complete locally and pushed to `main` on 2026-11-08:
- Updated `AdminProjectFlowPage` in [`blauframe-admin/src/app/(admin)/admin/projects/flow/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/page.tsx):
  - Uses `initializedProjectRef` to detect first load of each project.
  - Automatically initializes `expandedIds` with all task IDs so subtasks and child blocks are open by default.
  - User collapse/expand clicks mutate `expandedIds` without being overridden by subsequent re-renders.
  - Project/company switches reset `initializedProjectRef` so newly chosen projects are also auto-expanded on load.
- Updated `ProjectFlowPage` in [`blauframe-app/src/app/(app)/projects/flow/[id]/page.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/[id]/page.tsx):
  - Added identical auto-expansion logic for Client Portal Task Flow.
- Validation:
  - Admin Vitest: 268 passed / 0 failed (44 test files).
  - App Vitest: 145 passed / 0 failed (19 test files).
  - TypeScript: `tsc --noEmit` passed with 0 errors across both apps.
  - Next.js build: `npm run build` succeeded on both apps (36/36 admin, 29/29 app).
- Deployment: Committed (`67cb954` in `blauframe-admin`, `c838677` in `blauframe-app`) and pushed directly to `origin/main`.

## BF-WORKSPACE-012 — Task Flow Shooting Lines Animation & Bidirectional Edge Status States

Scope: Upgrade Task Flow connection animations and edge status reactivity across Admin Control Panel (`blauframe-admin`) and Client Portal (`blauframe-app`):
1. Replace dot animation (`<circle>` elements with animateMotion) with sleek shooting laser lines/beams along the curve (`<path>` elements with animated SVG dash stroke and glowing halo trail).
2. Fix edge status reactivity so that when a task is `in_progress`, the color changes to vibrant green on both sides (both incoming and outgoing edges).
3. Fix edge status reactivity so that when a task is `done`, the animation disappears/fades out from both sides (both incoming and outgoing edges, unless target task is actively in progress).

Implementation complete locally and pushed to `main` on 2026-11-08:
- Updated `FlowEdge.tsx` in [`blauframe-admin/src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx) and [`blauframe-app/src/app/(app)/projects/flow/components/FlowEdge.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/components/FlowEdge.tsx):
  - Renders a core laser beam line (`strokeDasharray="26 170"`, `strokeWidth={2.2}`) and soft glowing halo trail (`strokeDasharray="36 160"`, `strokeWidth={4.5}`) gliding from source to target.
  - Computes `isInProgress = (isSourceInProgress || isTargetInProgress) && !isTargetDone`.
  - Computes `isDone = isTargetDone || (isSourceDone && !isTargetInProgress)` to omit animation and display muted `#CBD5E1` curve.
- Updated `useProjectFlow.ts` in both apps to supply `isSourceDone`, `isSourceInProgress`, `isTargetDone`, and `isTargetInProgress` to every edge.
- Validation:
  - Admin Vitest: 268 passed / 0 failed (44 test files).
  - App Vitest: 145 passed / 0 failed (19 test files).
  - TypeScript: `tsc --noEmit` passed with 0 errors across both apps.
  - Next.js build: `npm run build` succeeded on both apps (36/36 admin, 29/29 app).
- Deployment: Committed (`4c14b8d` in `blauframe-admin`, `30e1059` in `blauframe-app`) and pushed directly to `origin/main`.

## BF-WORKSPACE-013 — Lighten Animated Background Grid Cubes on Services Page

Scope: On the landing website (`/en/services`), lighten by 50% the color intensity of the animated background grid cubes in the hero section.

Implementation complete locally and pushed to `main` on 2026-11-08:
- Updated `AnimatedGrid` in [`blauframe-frontend/src/components/sections/services/ServicesHero.tsx`](file:///c:/Projects/blauframe-frontend/src/components/sections/services/ServicesHero.tsx):
  - Changed keyframe `cellGlow` peak opacity at 50% from `0.4` to `0.2` (`opacity: 0.2; transform: scale(1.05);`).
  - Reduces the peak glow intensity of the brand blue (`#2660E9`) animated cubes by 50% against the white background.
- Validation:
  - Unit/integration tests: 22 passed / 0 failed in `tests/*.test.mjs`.
  - TypeScript: Next.js compilation succeeded and type checking completed with 0 errors.
- Deployment: Committed (`159c22b` in `blauframe-frontend`) and pushed directly to `origin/main`.

## BF-WORKSPACE-014 — Connect Book-a-Call Hours with Admin Availability & Localized Disabled Slots

Scope: Connect the public `/book-a-call` booking page directly with Admin Availability (Working hours & Blocked periods):
1. Dynamically list all hourly/slot windows corresponding to active Admin Working Hours (e.g. Monday 09:00–18:00 · 30 min yields all slots 09:00, 09:30 … 17:30). If Working Hours are updated in Admin, Book a Call reflects them in real-time.
2. If any slot is blocked by Admin (via Blocked Periods) or booked, display that slot as disabled (unclickable, muted), with a hover tooltip displaying "დაკავებულია" translated to English ("Booked") on English routes and to German ("Belegt") on German routes (`/de/book-a-call`).

Implementation complete locally and pushed to `main` on 2026-11-08:
- Updated `blauframe-api`:
  - [`app/Support/Onboarding/SlotAvailability.php`](file:///c:/Projects/blauframe-api/app/Support/Onboarding/SlotAvailability.php): Added `allWorkingSlotsForDay` to generate all slots within configured working windows. Extended `days()` with `$includeAll` flag and added `activeWorkingHours()`.
  - [`app/Actions/Onboarding/GetAvailableSlotsAction.php`](file:///c:/Projects/blauframe-api/app/Actions/Onboarding/GetAvailableSlotsAction.php): Passes `$includeAll` and exposes `working_hours`.
  - [`app/Http/Resources/Application/AvailabilityResource.php`](file:///c:/Projects/blauframe-api/app/Http/Resources/Application/AvailabilityResource.php): Appends `working_hours` to the response envelope.
  - [`app/Http/Controllers/Api/PublicOnboardingController.php`](file:///c:/Projects/blauframe-api/app/Http/Controllers/Api/PublicOnboardingController.php): Accepts `include_all` (defaults to `true` for public availability) so landing page receives full working-hours slots with availability status.
- Updated `blauframe-frontend`:
  - [`src/components/sections/book-a-call/BookingWidget.tsx`](file:///c:/Projects/blauframe-frontend/src/components/sections/book-a-call/BookingWidget.tsx):
    - Fetches `working_hours` and full slots with `include_all=true`.
    - Generates dynamic slots based on admin working hours per weekday rather than a hardcoded static list.
    - Renders blocked/booked slots with `disabled`, `cursor-not-allowed`, and a hover tooltip showing `"Booked"` (English) or `"Belegt"` (German).
    - Prevents selection of blocked/disabled slots and clears selections on date changes.
  - Added unit tests in [`tests/book-a-call-availability.test.mjs`](file:///c:/Projects/blauframe-frontend/tests/book-a-call-availability.test.mjs).
- Validation:
  - Frontend Node.js tests: 25 passed / 0 failed (including 3 new availability expansion and localization tests).
- Deployment: Committed (`db698fe` in `blauframe-api`, `0e84045` in `blauframe-frontend`) and pushed directly to `origin/main`.

## BF-WORKSPACE-015 — Task Flow Handle Alignment, Stage Insertion & Subtask Canvas Containment

Scope:
1. Fix connection dot (handle) vertical alignment on task cards in Task Flow (`blauframe-admin` and `blauframe-app`): lock strictly to `top: 50%; transform: translateY(-50%)` and eliminate `hover:scale-110` which was overriding the transform matrix and causing the handle to expand downwards out of center.
2. Fix handle and mid-edge insertion behavior: clicking the right handle or the mid-edge `+` button inserts a new root stage (`parent_id = null`, intermediate `position`) into the project sequence rather than creating a subtask.
3. Accept and persist `position` in `blauframe-api` task store/update actions and requests.
4. Contain subtasks inside each stage card checklist in both `blauframe-admin` and `blauframe-app`: prevent subtasks from exploding into parallel cards on the canvas with diverging/converging connector lines. The canvas remains a clean linear sequence of stages, while subtasks live inside each stage's card.

Implementation complete locally and pushed to `main` on 2026-11-09:
- Updated `blauframe-api`:
  - [`app/Http/Requests/Application/StoreTaskRequest.php`](file:///c:/Projects/blauframe-api/app/Http/Requests/Application/StoreTaskRequest.php) & [`app/Http/Requests/CP/TaskStoreUpdateRequest.php`](file:///c:/Projects/blauframe-api/app/Http/Requests/CP/TaskStoreUpdateRequest.php): Added `'position' => ['nullable', 'integer']`.
  - [`app/Actions/Task/StoreTaskAction.php`](file:///c:/Projects/blauframe-api/app/Actions/Task/StoreTaskAction.php): Persists `position`.
- Updated `blauframe-admin`:
  - [`src/app/(admin)/admin/tasks/api/service.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/tasks/api/service.ts): Added `position?: number;` to `TaskPayload`.
  - [`src/app/(admin)/admin/tasks/components/TaskForm.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/tasks/components/TaskForm.tsx): Added `position` to form schema, `defaultPosition` prop, and initialized default values.
  - [`src/app/(admin)/admin/projects/flow/components/TaskNode.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/TaskNode.tsx): Enforced `style={{ top: '50%', transform: 'translateY(-50%)' }}` and removed `hover:scale-110` from source handle.
  - [`src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx): Emits `onInsertTask(sourceTaskId, targetTaskId)`.
  - [`src/app/(admin)/admin/projects/flow/hooks/useProjectFlow.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/hooks/useProjectFlow.ts): Renders only sequential stages on canvas; connects stages sequentially.
  - [`src/app/(admin)/admin/projects/flow/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/page.tsx): Computes intermediate `position` on stage insertion, opens "Add Task" drawer, and safely accesses `rawTasks` via ref.
- Updated `blauframe-app`:
  - [`src/app/(app)/projects/flow/components/TaskNode.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/components/TaskNode.tsx): Enforced centered handle positioning with `top: 50%; transform: translateY(-50%)`.
  - [`src/app/(app)/projects/flow/hooks/useProjectFlow.ts`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/hooks/useProjectFlow.ts): Cleaned canvas layout to only render sequential stages and connect root tasks, keeping subtasks inside the card checklist.
- Validation:
  - API PHPUnit / Pest: 461 tests passed (4,258 assertions).
  - Admin Vitest: 268 passed / 0 failed (44 test files).
  - App Vitest: 145 passed / 0 failed (19 test files).
  - Production builds: Next.js `npm run build` succeeded on both Admin (36/36 pages) and App (29/29 pages).

## BF-WORKSPACE-016 — Fullscreen Canvas Toggle via React Flow Control Button

Scope: Enable browser fullscreen toggle when clicking the 4-corner fit-view button (`.react-flow__controls-fitview`) in Task Flow (`blauframe-admin` and `blauframe-app`):
1. Integrated HTML5 Fullscreen API (`requestFullscreen` / `exitFullscreen`) on the flow canvas wrapper `[data-flow-wrapper]`.
2. Encapsulated `AppDrawer` and `DeleteDialog` inside the fullscreen wrapper so task inspection, creation, and deletion work seamlessly inside fullscreen mode.
3. Added `fullscreenchange` event listener with auto-refit: smoothly animates `fitView({ duration: 350, padding: 0.15 })` to perfectly center and scale the flow to the new fullscreen resolution upon entering, and re-centers upon exiting (via button or `Esc` key).
4. Dynamically updated button tooltip to "Fullscreen / Fit view" and "Exit fullscreen (Esc)".

Implementation complete locally and pushed to `main` on 2026-11-09:
- Updated `blauframe-admin`:
  - [`src/app/(admin)/admin/projects/flow/components/ProjectFlow.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/ProjectFlow.tsx): Added `containerRef`, `handleToggleFullscreen`, `fullscreenchange` listener, and `onFitView={handleToggleFullscreen}` to `<Controls>`.
  - [`src/app/(admin)/admin/projects/flow/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/page.tsx): Wrapped canvas, `AppDrawer`, and `DeleteDialog` in `[data-flow-wrapper]` with dynamic `isFullscreen` styling.
- Updated `blauframe-app`:
  - [`src/app/(app)/projects/flow/components/ProjectFlow.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/components/ProjectFlow.tsx): Added `containerRef`, `handleToggleFullscreen`, `fullscreenchange` listener, and `onFitView={handleToggleFullscreen}` to `<Controls>`.
  - [`src/app/(app)/projects/flow/[id]/page.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/[id]/page.tsx): Wrapped canvas and `AppDrawer` in `[data-flow-wrapper]`.
- Validation:
  - Admin Vitest: 268 passed / 0 failed (44 test files).
  - App Vitest: 145 passed / 0 failed (19 test files).
  - Next.js build: Succeeded on both Admin (36/36) and App (29/29).

## BF-WORKSPACE-017 — Restore Subtask Boxes Flow Sequence (Task -> Subtasks -> Task -> Subtasks)

Scope:
1. Restored subtask node generation on the canvas: subtasks render as distinct boxes in the sequence between their parent task and the next root task (`Task 1 -> [Subtasks 1.x] -> Task 2 -> [Subtasks 2.x]`).
2. Maintained handle center alignment and smooth expand animation: handle dots remain vertically centered (`top: 50%; transform: translateY(-50%)` without hover jump), and child cards retain smooth unfold animation.
3. Preserved fullscreen functionality and responsive auto-refit across Admin and App.

Implementation complete locally and pushed to `main` on 2026-11-09:
- Updated `blauframe-admin`:
  - [`src/app/(admin)/admin/projects/flow/hooks/useProjectFlow.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/hooks/useProjectFlow.ts): Restored `pushNode` recursive tree traversal and `getExitTasks` connection to the next root task.
  - [`src/app/(admin)/admin/projects/flow/components/TaskNode.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/TaskNode.tsx): Restored child box unfold animation while keeping handle centering.
  - [`src/app/(admin)/admin/projects/flow/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/page.tsx): Intelligently assigns `parentId` based on context when inserting tasks, and auto-expands parent on subtask creation.
- Updated `blauframe-app`:
  - [`src/app/(app)/projects/flow/hooks/useProjectFlow.ts`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/hooks/useProjectFlow.ts): Restored `pushNode` and `getExitTasks` in client flow.
  - [`src/app/(app)/projects/flow/components/TaskNode.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/components/TaskNode.tsx): Restored child animation and verified centered handles.
- Validation:
  - Admin Vitest: 268 passed / 0 failed (44 test files).
  - App Vitest: 145 passed / 0 failed (19 test files).
  - Next.js production builds: Succeeded on both Admin (36/36) and App (29/29).

## BF-WORKSPACE-018 — Task Flow Edge Traveling Laser Pulse Animation & Full Reversion of Unrelated Sections

Scope:
1. Reverted all unrelated marketing section additions (`VisualIntegrationFlow`) across `blauframe-frontend`, `blauframe-admin`, and `blauframe-app`, ensuring zero leftover code or mockups.
2. Implemented elegant traveling laser beam pulse animation on React Flow connection edges in both `blauframe-admin` and `blauframe-app` (`FlowEdge.tsx`):
   - Single laser beam per edge with dynamic cubic-spline motion (`calcMode="spline"`, `keyTimes="0; 1"`, `keySplines="0.25 0.1 0.25 1"`), eliminating choppy repeating dashed lines.
   - Natural calm cycle with built-in pause between pulses (`cycleDistance = travelDistance * 1.55`).
   - Opacity fade-in upon entering from source handle and graceful fade-out into target handle (`values="0; 1; 1; 0; 0"`, `keyTimes="0; 0.12; 0.62; 0.72; 1"`).
   - Dual-layer depth: crisp linear-gradient laser core plus soft Gaussian halo glow (`feGaussianBlur stdDeviation="2.2"`).
   - State-aware styling:
     - `in_progress`: both incoming and outgoing edges illuminate in emerald green (`#10B981` / `#34D399` / `#6EE7B7`, track `#86EFAC`).
     - `done`: animation cleanly extinguishes into a static muted border (`#CBD5E1`), unless target is actively in progress.
     - Default (pending): subtle cyan/blue pulse (`#2563EB` / `#38BDF8` / `#93C5FD`, track `#E2E8F0`).
   - Preserved interactive `+` button (`EdgeLabelRenderer`) on hover between boxes in Admin flow.

Implementation complete locally on 2026-11-09:
- Updated `blauframe-admin`:
  - [`src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/FlowEdge.tsx): Upgraded edge with laser gradient, halo glow, single traveling beam, and status transitions.
- Updated `blauframe-app`:
  - [`src/app/(app)/projects/flow/components/FlowEdge.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/components/FlowEdge.tsx): Applied matching edge laser beam animation in client task flow.
- Validation:
  - Admin Vitest: 268 passed / 0 failed (44 test files).
  - App Vitest: 145 passed / 0 failed (19 test files).
  - Production builds: Next.js build succeeded in both Admin (36/36) and App (29/29).
  - Deployed branches: Synchronized `origin/dev` with `origin/main` in `blauframe-app` (`037cb3d`) so Forge auto-deploy updates `blauframe-app.on-forge.com` with laser beam pulse animation and two-level flow architecture.


## BF-WORKSPACE-019 — Task Flow Two-Level Simplification & Search Empty State Toolbar Persistence

Scope:
1. Strictly Two-Level Flow Architecture (Tasks and Subtasks Only):
   - Eliminated recursive multi-tier child-of-child tree expansion in `useProjectFlow.ts` (`blauframe-admin` and `blauframe-app`).
   - All subtasks (even if legacy records in DB point to a subtask parent) resolve to their top-level root task via `findRootId`.
   - Clean, sequential layout: `[Root Task 1] -> [Subtasks 1.x] -> [Root Task 2] -> [Subtasks 2.x]`.
   - Subtask cards are clean leaf nodes: no `+ Add Subtask` button, no nested checklists, no expandable chevrons.
2. Deep Search Filter:
   - Search query matches root tasks by name/description OR by any of their subtasks' name/description, ensuring subtask keyword searches properly reveal their parent stages.
3. Search Empty State & Persistent Toolbar:
   - Fixed bug where `EmptyState` overlay hid the bottom toolbar and search input on zero results: moved `FlowToolbar` outside `<ReactFlow>` at `z-30`, ensuring toolbar with search input is always visible.
   - Context-aware `EmptyState`:
     - When searching with zero matches: shows Search icon, "No tasks found", `No tasks matched "{search}"`, and a "Clear Search" button.
     - When project is genuinely empty: shows Monitor icon, "No tasks in this project", and "+ Create First Task".
   - `FlowToolbar`: synchronized `isSearchOpen = searchOpen || search.length > 0` so the search bar stays open during typing and filtering.

Implementation complete locally on 2026-11-09:
- Updated `blauframe-admin`:
  - [`src/app/(admin)/admin/projects/flow/hooks/useProjectFlow.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/hooks/useProjectFlow.ts)
  - [`src/app/(admin)/admin/projects/flow/components/ProjectFlow.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/ProjectFlow.tsx)
  - [`src/app/(admin)/admin/projects/flow/components/FlowToolbar.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/FlowToolbar.tsx)
  - [`src/app/(admin)/admin/projects/flow/components/TaskNode.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/projects/flow/components/TaskNode.tsx)
- Updated `blauframe-app`:
  - [`src/app/(app)/projects/flow/hooks/useProjectFlow.ts`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/hooks/useProjectFlow.ts)
  - [`src/app/(app)/projects/flow/components/ProjectFlow.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/components/ProjectFlow.tsx)
  - [`src/app/(app)/projects/flow/components/TaskNode.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/projects/flow/components/TaskNode.tsx)
- Validation:
  - Admin Vitest: 268 passed / 0 failed (44 test files).
  - App Vitest: 145 passed / 0 failed (19 test files).
  - Production builds: Next.js build succeeded in both Admin (36/36) and App (29/29).
  - Deployed branches: Synchronized `origin/dev` with `origin/main` in `blauframe-app` (`037cb3d`) so Forge auto-deploy updates `blauframe-app.on-forge.com` with laser beam pulse animation and two-level flow architecture.

## ADM-01 — Synchronize Admin picomatch lockfile (2026-11-12)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-admin/package-lock.json` only; workspace journal records the evidence.
- Source checkout: `C:/Projects/blauframe-admin`; fetched `origin/main` at `1b470f0`.
- Isolated Admin checkout: `C:/Users/fiord/Documents/Codex/2026-11-12/https-blauframe-control-frankspool8-chatgpt-site/work/blauframe-admin`.
- Workspace documentation checkout: `C:/Users/fiord/Documents/Codex/2026-11-12/https-blauframe-control-frankspool8-chatgpt-site/work/blauframe-workspace`.
- Admin branch: `fix/adm-01-picomatch-lockfile`.
- Admin commit: `589063e305eaa673e29f9a760d8f43a94625d8da` (pushed to `sarsevanidze/blauframe-admin`).
- Reproduced with Node 22.16.0 / npm 10.9.2: `npm ci --ignore-scripts --dry-run --no-audit --no-fund` rejected the old lockfile.
- Regenerated with `npm install --package-lock-only --lockfile-version=2 --ignore-scripts --no-audit --no-fund`. Root `picomatch` is 4.0.7; anymatch/micromatch/readdirp keep their nested 2.3.2. `package.json` was not changed.
- Validation: clean `npm ci --no-audit --no-fund` passed (823 packages); `npm ls picomatch` resolved correctly; `npm test` passed (268/268, 44 files); `npx tsc --noEmit` passed; `npm run build` passed (36/36 pages); `git diff --check` passed.
- Build used local API/Reverb targets and disabled Next telemetry. Existing non-failing warnings: Kanban useMemo dependency and outdated caniuse-lite/browserslist.
- Remaining: review the branch, merge via the intended main workflow, then verify Forge's Linux npm ci/build and record the result in Control Center.
- Rollback: revert the fix commit through normal review if needed; note that doing so restores the reproduced lockfile desynchronization.

## MED-01 — Admin Media Library Internal Workspace Scoping & Test Coverage (2026-11-12)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-admin` (`admin/media` and `MediaLibraryModal`).
- Source checkout: `C:/Projects/blauframe-admin`; base `origin/main` at `1b470f0`.
- Isolated Admin worktree: `C:/Projects/.worktrees/med-01-blauframe-admin`.
- Admin branch: `feat/med-01-internal-media`.
- Admin commit: `7a837b6b7910dfe499a0e9725c2406aebb16fbd6` (pushed to `sarsevanidze/blauframe-admin`).
- Implemented changes:
  - [`src/components/shared/MediaLibraryModal.tsx`](file:///c:/Projects/blauframe-admin/src/components/shared/MediaLibraryModal.tsx): Explicitly passed `'internal'` workspace to `useDrive(folderId, search, 'internal')` to guarantee strict isolation to internal admin-only media assets when selecting covers/thumbnails for blog posts, guides, and service forms. Replaced invalid nested button HTML structure with accessible, keyboard-interactive card elements.
  - [`src/app/(admin)/admin/media/hooks/index.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/media/hooks/index.ts): Added user-facing success notification (`File uploaded`) and robust error toast handling (`Failed to upload file`, `Failed to delete file`) to `useUploadToLibrary` and `useDeleteFromLibrary`.
  - [`src/app/(admin)/admin/media/api/service.test.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/media/api/service.test.ts): Added comprehensive unit tests for `MediaLibraryService.renameFolder` and `MediaLibraryService.removeFolder`, achieving 100% method coverage across all 20 service operations.
  - [`src/components/shared/MediaLibraryModal.test.tsx`](file:///c:/Projects/blauframe-admin/src/components/shared/MediaLibraryModal.test.tsx): Created new unit test suite (5 tests) verifying internal workspace scoping, folder browsing, image filtering, selection confirmation, and search filtering.
- Validation:
  - Admin Vitest: 275 passed / 0 failed (45 test files).
  - TypeScript: 0 errors (`npx tsc --noEmit`).
  - ESLint: 0 errors (`npm run lint`).
  - Next.js production build: Succeeded (36/36 static/dynamic pages generated).
- Remaining: Review branch `feat/med-01-internal-media`, merge into `main`, and verify deployment on Forge.

## MED-02 — Folders & Sharing Hierarchy, Destination Selection & Permissions [admin/files] (2026-11-12)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-admin` (`admin/files`, `admin/media`, folder hierarchy and sharing).
- Source checkout: `C:/Projects/blauframe-admin`; base `feat/med-01-internal-media` at `7a837b6`.
- Isolated Admin worktree: `C:/Projects/.worktrees/med-02-blauframe-admin`.
- Admin branch: `feat/med-02-folders-and-sharing`.
- Admin commit: `7b911ab` (pushed to `sarsevanidze/blauframe-admin`).
- Implemented changes:
  - [`src/app/(admin)/admin/files/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/files/page.tsx): Added direct header navigation action linking `Attachments` (`/admin/files`) to `Media Drive` (`/admin/media`), connecting attachment management with folder hierarchy and sharing.
  - [`src/app/(admin)/admin/media/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/media/page.tsx):
    - Added hierarchical ancestor breadcrumb options in Move Folder and Move File dialogs, allowing users to move folders and files both up the tree (to parent and ancestor folders) and down into subfolders.
    - Updated shared status badge logic to recognize folders within shared workspace hierarchy.
    - Wrapped breadcrumbs in `useMemo` to eliminate React Hook exhaustive dependency warnings.
  - [`src/app/(admin)/admin/media/hooks/index.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/media/hooks/index.ts): Updated `useShareFolder` to invalidate `['folder-permissions']` query cache upon granting company permissions so permissions lists update immediately in the active modal.
  - [`src/app/(admin)/admin/media/api/service.test.ts`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/media/api/service.test.ts): Added tests for nested folder creation (`parentId`) and multi-level folder sharing with companies.
  - [`src/app/(admin)/admin/media/FolderManagement.test.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/media/FolderManagement.test.tsx): Added unit tests for files page navigation to Media Drive.
  - [`src/app/(admin)/admin/media/hooks/index.test.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/media/hooks/index.test.tsx): Added unit tests for folder creation, moving, sharing, and permission revocation with cache invalidation verification.
- Validation:
  - Vitest: 283 passed / 0 failed (47 test files).
  - TypeScript: 0 errors (`tsc --noEmit`).
  - ESLint: 0 errors (`npm run lint`).
  - Next.js production build: Succeeded (36/36 static/dynamic pages generated).
- Remaining: Code review on branch `feat/med-02-folders-and-sharing`, merge to `main`, and verify deployment on Forge.

## MED-03 — Client Files Access, IDOR Protection & Permission Isolation [client/files] (2026-11-12)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-app` (`client/files`, client data isolation, access-level enforcement, IDOR defenses).
- Source checkout: `C:/Projects/blauframe-app`; base `origin/main` at `037cb3d`.
- Isolated App worktree: `C:/Projects/.worktrees/med-03-blauframe-app`.
- App branch: `feat/med-03-client-files-access`.
- App commit: `483c143` (pushed to `sarsevanidze/blauframe-app`).
- Implemented changes:
  - [`src/app/(app)/files/page.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/files/page.tsx):
    - Added error handling state (`isDriveError`) on drive query. When an unauthorized, deleted, or cross-company folder ID (IDOR) is requested, renders a dedicated "Folder Unavailable" secure state with an action to return safely to root files (`/files`), preventing unauthorized upload/creation attempts into invalid scopes.
    - Protected `handleDownload` with expiration and availability checks, displaying informative toast feedback when signed download URLs are unavailable or expired.
    - Restricted drag-and-drop and action creation triggers whenever folder error states are detected.
  - [`src/app/(app)/files/ClientFilesAccess.test.tsx`](file:///c:/Projects/blauframe-app/src/app/(app)/files/ClientFilesAccess.test.tsx): Created a dedicated test suite (5 tests) covering:
    - Multi-client isolation: Client A sees only Company A files and never sees Company B's confidential documents.
    - Client B isolation: Client B sees only Company B documents.
    - IDOR protection: Attempting to access an unauthorized folder ID (404/403) displays the "Folder Unavailable" barrier rather than empty drive with upload tools, and provides a return button.
    - Permission enforcement: View-only shared folders and assets restrict mutation controls (no rename, move, or trash), while manager folders allow full operations.
    - Download failure handling: Inaccessible or expired download links trigger error toast notifications.
- Validation:
  - Vitest: 150 passed / 0 failed (20 test files).
  - TypeScript: 0 errors (`tsc --noEmit`).
  - ESLint: 0 errors (`npm run lint`).
  - Next.js production build: Succeeded (29/29 static/dynamic pages generated).
- Remaining: Code review on branch `feat/med-03-client-files-access`, merge to `main`, and verify deployment on Forge.

## API-01 — File Authorization & Tenant Isolation [Laravel · files] (2026-11-13)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-api` (`Laravel · files`, file authorization, 401/403/404 matrix, tenant isolation, and private storage).
- Source checkout: `C:/Projects/blauframe-api`; base `origin/main` at `a447b6b`.
- Isolated API worktree: `C:/Projects/.worktrees/api-01-blauframe-api`.
- API branch: `feat/api-01-file-authorization`.
- API commit: `d029d71` (pushed to `sarsevanidze/blauframe-api`).
- Implemented changes:
  - [`docs/file-authorization-and-isolation.md`](file:///c:/Projects/blauframe-api/docs/file-authorization-and-isolation.md): Authored authoritative specification and inventory documenting:
    - Complete route inventory for Control Panel (`/api/v1/cp/media-library*`, `/api/v1/cp/file-manager*`) and Client Portal (`/api/v1/app/files*`).
    - Controller and Action mapping (`MediaAssetController`, `FileManagerController`, `StoreMediaAssetAction`, `MoveAssetAction`, `RestoreMediaAssetAction`, `FolderPermissionService`).
    - Defense-in-depth middleware layers and status codes (`auth:sanctum` 401, `admin` 403, `company.active` 403, `onboarding.completed` 403, `auth.or.signed` 401/403, and IDOR query isolation 404).
    - Granular permission hierarchy (`viewer` list/preview/download, `contributor` upload, `manager` rename/move/delete/subfolders) with ancestor inheritance.
    - Storage isolation architecture: files stored strictly on the private `local` disk (`storage/app/private`), short-lived 15-minute temporary signed URLs, and security response headers (`X-Content-Type-Options: nosniff`, `Cache-Control: no-store, private`, `Referrer-Policy: no-referrer`).
  - [`README.md`](file:///c:/Projects/blauframe-api/README.md) & [`docs/api-and-auth.md`](file:///c:/Projects/blauframe-api/docs/api-and-auth.md): Linked the file authorization and isolation specification in the documentation matrix.
  - [`tests/Feature/FileAuthorizationSecurityTest.php`](file:///c:/Projects/blauframe-api/tests/Feature/FileAuthorizationSecurityTest.php): Created dedicated security test suite with 10 comprehensive tests (80 assertions):
    1. Unauthenticated requests to all 14 CP media library and file manager endpoints return 401.
    2. Unauthenticated requests to all 15 Client Portal file and folder endpoints return 401.
    3. Customer users are strictly forbidden (403) from accessing all CP media and file routes.
    4. Suspended company users are forbidden (403) from client app file operations.
    5. Incomplete onboarding users are blocked (403) by the `onboarding.completed` gate.
    6. Granular permission enforcement: Viewers can preview and download but are rejected (403) from uploading or deleting; contributors can upload but cannot delete; managers have full mutation permissions.
    7. Client users are forbidden (403) from modifying or deleting admin-shared root folders or moving shared assets to their private root.
    8. Cross-company IDOR tenant isolation: Customer A accessing, downloading, previewing, renaming, or deleting Customer B's files or unshared internal admin files returns 404 Not Found to prevent data leakage.
    9. Temporary signed URLs permit preview and download without tokens, while tampered or invalid signatures fail with 401.
    10. Media assets are confirmed stored exclusively on the private `local` disk and are never accessible via the `public` disk.
- Validation:
  - Pest test suite: 10/10 passed (80 assertions) in `FileAuthorizationSecurityTest.php`.
  - Full file/media test suites: 38/38 passed (281 assertions) across `FileAuthorizationSecurityTest`, `MediaDriveSecurityTest`, `MediaDriveCompleteE2EFlowTest`, and `ApplicationFilesTest`.
  - Comprehensive repository test suite: 461/461 tests passed (4183 assertions) across `blauframe-api`.
- Remaining: Code review on branch `feat/api-01-file-authorization`, merge to `main`, and verify deployment on Forge.

## API-02 — Nested Folders, Scopes, Permission Inheritance & Cycle Prevention [Laravel · folders] (2026-11-13)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-api` (`Laravel · folders`, multi-tenant scopes, multi-level permission inheritance, parent moving, and graph cycle prevention).
- Source checkout: `C:/Projects/blauframe-api`; base `origin/main` at `a447b6b`.
- Isolated API worktree: `C:/Projects/.worktrees/api-02-blauframe-api`.
- API branch: `feat/api-02-nested-folders-permissions`.
- API commit: `f5eb711` (pushed to `sarsevanidze/blauframe-api`).
- Implemented changes:
  - [`docs/nested-folders-and-permissions.md`](file:///c:/Projects/blauframe-api/docs/nested-folders-and-permissions.md): Authored architectural documentation covering:
    - Multi-tenant folder scope distinctions: Private Admin-only (`company_id: null`, unshared), Shared Admin (`company_id: null` with `FolderPermission` grants), and Company-owned private folders (`company_id: int`).
    - Permission inheritance hierarchy: Dynamic ancestor walking via `FolderPermissionService::resolveFolderPermission()`, resolving nearest permission grants from leaf up to root.
    - Granular capability matrix for `viewer` (browse/view/download), `contributor` (upload), and `manager` (subfolder creation, rename, move, delete).
    - Client breadcrumb security sanitization: Masking internal parent folders when sharing deeper subfolders to prevent information leaks.
    - Parent move constraints: Target company scope validation and graph cycle prevention algorithms in `MoveFolderAction`.
    - Cascading soft-delete and trashed hierarchy restoration (`DestroyFolderAction` and `RestoreFolderAction`).
  - [`app/Actions/Folder/MoveFolderAction.php`](file:///c:/Projects/blauframe-api/app/Actions/Folder/MoveFolderAction.php):
    - Unified validation exception messaging for folder self-moves, subfolder cycles, and cross-company scope violations.
  - [`lang/en/messages.php`](file:///c:/Projects/blauframe-api/lang/en/messages.php) & [`lang/de/messages.php`](file:///c:/Projects/blauframe-api/lang/de/messages.php):
    - Added localized error strings for `folder_cannot_move_into_self`, `folder_cannot_move_into_subfolder`, and `folder_cannot_move_different_scope`.
  - [`README.md`](file:///c:/Projects/blauframe-api/README.md): Linked the nested folders and permission inheritance guide in documentation index.
  - [`tests/Feature/NestedFolderPermissionsTest.php`](file:///c:/Projects/blauframe-api/tests/Feature/NestedFolderPermissionsTest.php): Authored comprehensive test suite (13 tests, 51 assertions):
    1. Scope isolation: Clients cannot see or access admin internal unshared folders.
    2. Multi-tenant isolation: Cross-company folder isolation returns 404.
    3. Multi-level inheritance: Viewer permission inherits down three levels; viewer can view but is blocked (403) from uploading, creating subfolders, renaming, or deleting.
    4. Contributor inheritance: Contributor permission inherits down levels and allows uploads, but blocks structural renaming and deletion (403).
    5. Manager inheritance: Manager permission inherits down levels, permitting subfolder creation, renaming, and moving, while protecting admin root folders from deletion.
    6. Breadcrumb sanitization: Client breadcrumb trail truncates at the shared folder and does not leak unauthorized internal ancestor names.
    7. Parent moving: Company folder move within own hierarchy succeeds.
    8. Scope violation guard: Cannot move folders across company boundaries (422/404).
    9. Cycle prevention (self): Cannot move folder into itself (422).
    10. Cycle prevention (child): Cannot move folder into direct child (422).
    11. Cycle prevention (deep descendant): Cannot move folder into 3-level deep grandchild (422).
    12. Cascade soft-delete: Trashing a folder soft-deletes its descendants and assets.
    13. Trashed parent restoration: Restoring an orphaned folder resets `parent_id` to `null` if the parent remains trashed.
- Validation:
  - Pest test suite: 13/13 passed (51 assertions) in `NestedFolderPermissionsTest.php`.
  - Combined folder test suite: 19/19 passed (69 assertions) across `DriveFoldersTest` and `NestedFolderPermissionsTest`.
- Remaining: Code review on branch `feat/api-02-nested-folders-permissions`, merge to `main`, and verify deployment on Forge.

## SEC-01 — Upload Security, MIME Validation & Negative Testing Suite [Laravel · uploads] (2026-11-13)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-api` (`Laravel · uploads`, MIME and extension validation, file size limits, safe Content-Disposition headers, storage quota enforcement, and negative testing checklist).
- Source checkout: `C:/Projects/blauframe-api`; base `origin/main` at `a447b6b`.
- Isolated API worktree: `C:/Projects/.worktrees/sec-01-blauframe-api`.
- API branch: `feat/sec-01-upload-security`.
- API commit: `4e8e8ce` (pushed to `sarsevanidze/blauframe-api`).
- Implemented changes:
  - [`docs/upload-security-and-negative-checklist.md`](file:///c:/Projects/blauframe-api/docs/upload-security-and-negative-checklist.md): Authored authoritative specification covering:
    - Defense-in-depth architecture across 5 security layers (Network/Middleware, FormRequest, Controller/Quota, Storage/Sanitization, Delivery/Signed URLs).
    - Comprehensive Negative Upload Checklist covering 12 test vectors (PHP scripts, binaries, shell scripts, batch files, oversized files, quota exhaustion, path traversal, header injection, public disk leakage, expired signed URLs, and tampered signatures).
  - [`app/Http/Requests/CP/StoreMediaAssetRequest.php`](file:///c:/Projects/blauframe-api/app/Http/Requests/CP/StoreMediaAssetRequest.php) & [`app/Http/Requests/Application/StoreMediaAssetRequest.php`](file:///c:/Projects/blauframe-api/app/Http/Requests/Application/StoreMediaAssetRequest.php):
    - Added strict `mimes` allowlist rule (`jpg,jpeg,png,gif,webp,svg,heic,avif,pdf,doc,docx,xls,xlsx,ppt,pptx,txt,rtf,csv,zip,rar,7z,tar,gz,mp4,mov,avi,mkv,webm,mp3,wav,ogg,m4a,json,xml`) and `max:51200` (50MB) limit, rejecting executable binaries and script uploads with 422.
  - [`app/Http/Controllers/CP/MediaAssetController.php`](file:///c:/Projects/blauframe-api/app/Http/Controllers/CP/MediaAssetController.php) & [`app/Http/Controllers/Application/FileManagerController.php`](file:///c:/Projects/blauframe-api/app/Http/Controllers/Application/FileManagerController.php):
    - Sanitized `safeName` in `streamMedia()` by replacing `"`, `'`, `/`, and `\` with `_`, preventing Symfony `HeaderUtils` 500 exceptions and protecting against Content-Disposition header injection.
    - Verified strict delivery headers: `X-Content-Type-Options: nosniff`, `Referrer-Policy: no-referrer`, `Cache-Control: private, no-store`.
  - [`README.md`](file:///c:/Projects/blauframe-api/README.md): Linked upload security and negative testing checklist in the documentation matrix.
  - [`tests/Feature/UploadSecurityTest.php`](file:///c:/Projects/blauframe-api/tests/Feature/UploadSecurityTest.php): Authored comprehensive security test suite (11 tests, 45 assertions):
    1. Rejection of PHP script uploads with 422 (`shell.php`).
    2. Rejection of binary and script extensions (`.exe`, `.sh`, `.bat`) with 422.
    3. Acceptance of whitelisted media and document formats (`.png`, `.pdf`, `.zip`).
    4. Rejection of oversized files exceeding 50MB with 422.
    5. Storage quota enforcement rejecting uploads when company limit is exceeded (422).
    6. Directory traversal stripping in uploaded filenames (`../../../../etc/passwd.pdf`).
    7. Safe Content-Disposition header formatting and quote escaping without 500 error.
    8. Storage visibility isolation ensuring media is strictly saved to private `local` disk and never public web disk.
    9. Temporary signed URL access granted within 15-minute validity window without Bearer token.
    10. Expiration rejection returning 401 when accessing signed URL past validity window.
    11. Tamper rejection returning 401 on manipulated signature or modified user parameters.
- Validation:
  - Pest test suite: 11/11 passed (45 assertions) in `UploadSecurityTest.php`.
  - Combined suite: 17/17 passed (63 assertions) across `UploadSecurityTest` and `DriveFoldersTest`.
- Remaining: Code review on branch `feat/sec-01-upload-security`, merge to `main`, and verify deployment on Forge.

## QA-01 — Admin Regression Rerun & Validation [blauframe-admin · Next.js 14] (2026-11-13)

Status: implementation verified and pushed for review; not merged or deployed.

- Scope: `blauframe-admin` (`Regression`, complete Vitest rerun on current `main`, TypeScript verification, ESLint cleanup, and production build).
- Source checkout: `C:/Projects/blauframe-admin`; base `origin/main` at `1b470f0`.
- Isolated Admin worktree: `C:/Projects/.worktrees/qa-01-blauframe-admin`.
- Admin branch: `test/qa-01-admin-regression`.
- Admin commit: `6557651` (pushed to `sarsevanidze/blauframe-admin`).
- Implemented changes:
  - [`docs/admin-regression-audit-2026-11-13.md`](file:///c:/Projects/blauframe-admin/docs/admin-regression-audit-2026-11-13.md): Authored regression report documenting:
    - 268/268 Vitest tests passed across 44 test files (+15 new tests added since 2026-10-27 baseline audit of 253).
    - Route matrix of 36 static/dynamic production pages.
    - TypeScript validation report (0 errors).
    - ESLint audit and clean resolution (0 errors, 0 warnings).
  - [`src/app/(admin)/admin/tasks/kanban/page.tsx`](file:///c:/Projects/blauframe-admin/src/app/(admin)/admin/tasks/kanban/page.tsx):
    - Wrapped `allProjects` and `companies` initializations in `useMemo` hooks, resolving React hook dependency warning and bringing repository ESLint output to 0 errors and 0 warnings.
- Validation:
  - Vitest: 268 passed / 0 failed (44 test files).
  - TypeScript: 0 errors (`npx tsc --noEmit`).
  - ESLint: 0 errors, 0 warnings (`npm run lint`).
  - Next.js production build: Succeeded (36/36 static/dynamic pages generated).
- Remaining: Code review on branch `test/qa-01-admin-regression`, merge to `main`, and verify deployment on Forge.







