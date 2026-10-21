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
| Admin | `aa7be51` | Workspace instructions, corrected auth paths and realtime documentation |
| API | `a2092ea` | New AGENTS.md, safer setup documentation, role and login-envelope corrections |
| App | `c08d607` | Workspace instructions and documented API/realtime boundaries |
| Landing | `eb19253` | Build separated from CMS mutations, explicit deploy steps, media preservation tests and updated docs |

Validation:

- Root JSON, four separate Git boundaries, instruction references and selected
  documentation links checked. Ignore rules exclude all four apps, `.env`, unknown
  root files and additional project folders.
- Admin: 230 tests passed (39 files); TypeScript passed. Existing React `act()`
  warning observed in a hook test.
- App: 112 tests passed (15 files). Only documentation changed.
- Landing: 5 script tests passed, including a stub-compiler build boundary test,
  persistent upload collision preservation and Forge layout handling. Windows
  sandbox blocked a fixture rename; the same isolated tests passed outside it.
- Landing TypeScript and ESLint on the new scripts passed. Full repository ESLint
  reported 54 errors and 14 warnings in unchanged files; no lint-clean claim is made.
- API PHP tests were not run: PHP/Composer were not available on PATH; only docs
  and instructions changed.
- Full Next/Payload build, live migration, media deployment and DB content snapshot
  acceptance remain unexecuted. The script test deliberately stubs the compiler
  and is not a substitute for staging verification.

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

Shared docs describe existing behavior unless explicitly marked as a proposal.
