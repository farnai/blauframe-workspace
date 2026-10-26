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
