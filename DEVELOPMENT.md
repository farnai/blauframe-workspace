# Development and coordinated changes

## Environments

Run commands from their owning repository. Install JS dependencies with `npm ci`
using that repository's lockfile. Install PHP dependencies with `composer install`.
Use a Node version compatible with the locked packages (Node 22 used for this setup).
PHP requirements must also satisfy `composer.lock`, including development tools.

Do not copy `.env`, `node_modules`, lockfiles, or build outputs between repositories.
Create local environment files from each repository's example, using local targets.
The existing portal examples reference a deployed API; do not use that target for
local integration tests by accident.

| Service | Port | Local command / setting |
| --- | --- | --- |
| Business Admin | 3000 | `npm run dev` in `blauframe-admin` |
| Client Portal | 3001 | `npm run dev` in `blauframe-app` |
| Landing + CMS | 3002 | `npm run dev -- -p 3002` in `blauframe-frontend` |
| Laravel HTTP | 8000 | `php artisan serve --port=8000` in `blauframe-api` |
| Laravel Reverb | 8080 | `php artisan reverb:start` in `blauframe-api` |
| Laravel queue | n/a | `php artisan queue:listen --tries=1` in `blauframe-api` |
| Landing PostgreSQL | 5432 | Landing `docker compose up -d` |
| Business database | environment-specific | Separate database configured by Laravel |

`composer dev` starts HTTP, queue, logs, and Vite; it does not start Reverb.
Use separate terminals for these processes. Deployment ports are independently
configured; this table is the local development convention.

Both portals:

```dotenv
NEXT_PUBLIC_API_URL=http://localhost:8000/api
NEXT_PUBLIC_REVERB_HOST=127.0.0.1
NEXT_PUBLIC_REVERB_PORT=8080
NEXT_PUBLIC_REVERB_SCHEME=http
```

Set the Reverb public app key to match the local API. Set portal `NEXT_PUBLIC_APP_URL`
to its own origin if used. Landing uses `NEXT_PUBLIC_APP_URL=http://localhost:3002`,
its own PostgreSQL `DATABASE_URI`, and a stable `PAYLOAD_SECRET`. Its
`NEXT_PUBLIC_API_URL` currently helps configure image hosts; it is not proof of a
Laravel integration. See its updated README for the local Payload URL.

Laravel CORS already lists local ports 3000, 3001 and 3002. Recheck CORS, OAuth
callbacks, WebSocket origins and client URLs when changing hosts or ports.

## Working on a task

1. Inspect `git status --short --branch` for each affected repository. The initial
   App checkout was `dev`, while the other applications were `main`; do not assume
   a common base branch or switch away from another person's work.
2. Assign a work item ID and list affected repositories and contracts. For example,
   changing onboarding status may affect API, Admin and App, but not Landing.
3. Use a feature branch in each affected repository. For concurrent writers, create
   separate worktrees per repository and record exact paths. Provide each worktree
   task the absolute location of the workspace instructions.
4. Use dedicated DBs/media/ports for concurrent environments. The landing Compose
   file currently fixes container name `payload-postgres` and host port 5432;
   a second instance needs a reviewed override, not just a new worktree.
5. Implement compatible API additions before updating consumers. Remove old fields
   only after all consumers have migrated. Review schema migrations separately.
6. Run repository checks and the affected cross-application scenario. Review diffs
   and create separate commits/PRs linked by the work item ID.
7. Record tested commit combinations and deployment order. Code commits do not
   capture database contents; migration and rollback plans belong in the release.

## Checks

| Repository | Checks |
| --- | --- |
| Workspace | Parse JSON files; verify root ignore rules and staged file list; check documentation paths |
| Admin | `npm test`, `npx tsc --noEmit`; lint/build where relevant |
| App | `npm test`; typecheck/lint/build where relevant |
| API | `composer test` with a verified isolated test DB; PHP checks as appropriate |
| Landing | `npm run test:scripts`, `npm run lint`, `npx tsc --noEmit`; build with an isolated configured CMS DB |

API `phpunit.xml` declares in-memory SQLite. Verify actual test configuration before
running DB-changing tests. A passing mock-based portal test is not a contract test.

Useful integration acceptance scenarios:

- Admin creates a project; the correct customer sees it and another company cannot.
- Customer schedules onboarding; Admin sees/approves it; customer sees the change.
- A conversation message reaches its authorized recipients over Reverb.
- CMS editor saves a draft and custom booking text; a build preserves both.

Landing deployment now uses explicit migration and media preparation steps. Follow
`blauframe-frontend/DEPLOYMENT.md` before deploying that change. Build still may need
database reads for rendering; it must not invoke content maintenance scripts.
