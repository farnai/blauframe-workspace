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
| Admin | `aa7be51`, `ae7fbdc`, `61cf0ba`, `9169171` | Workspace instructions, corrected auth paths, realtime docs, dynamic dashboard metrics/charts/tasks, calendar navigation fix, user avatar display in UsersTable/Header/Sidebar, and explicit image removal in forms/ImageUpload/MediaImagePicker |
| API | `a2092ea`, `d64dec4`, `c6eb71f`, `49a158a`, `40608c4` | New AGENTS.md, role corrections, bidirectional synchronization between company/user tables on update, 2MB avatar upload/deletion endpoints, avatar column fallback in Application/CP UserResource, and explicit removal handling for user avatars, company logos, service images, blog images, and guide thumbnails |
| App | `c08d607`, `feb5376`, `7105f74`, `fc907c4`, `32bf369`, `37399f2` | Workspace instructions, calendar chevron positioning, onboarding projects gating, safe Echo handling, dynamic dashboard, live profile & company settings synchronization, avatar upload/remove UI with 2MB validation, direct header dropdown and sidebar links to Settings, synchronized on both main and dev branches |
| Landing | `eb19253`, `bbf45c9` | Build separated from CMS mutations, explicit deploy steps, media preservation tests, and full ESLint resolution (0 errors, 0 warnings) |

Validation:

- Admin: 230 tests passed (39 files); production build succeeded (36 pages). Dashboard metrics, charts, and active tasks hooked dynamically to backend APIs (`/v1/cp/dashboard/metrics`, `/v1/cp/dashboard/charts`, `/v1/tasks`); hardcoded mock metrics and sparklines removed; calendar navigation fixed.
- App: 112 tests passed (15 files); production build succeeded (29 pages). Dashboard metrics, charts, and active tasks hooked dynamically to backend; empty-state handling added for 0 completed tasks/activity; hardcoded percentage badges removed when uncomputed; recently viewed isolated per user ID; settings profile form dynamically bound with active company relation and window focus refetch.
- API: Bidirectional sync between company and users ensured in `UpdateCompanyAction`, `UpdateUserAction`, and `UpdateProfileAction`; `UserResource` falls back to `company` relation for `company_name`, `company_address`, `tax_id`, and `phone`.


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
  - Git: Synced `dev` and `main` branches and published to remote.

Shared docs describe existing behavior unless explicitly marked as a proposal.
