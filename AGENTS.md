# Blauframe workspace instructions

This is the coordination repository for four independent Git repositories. Read
`SYSTEM.md`, `DEVELOPMENT.md`, and the target repository's `AGENTS.md` before editing.
The repository-specific instructions govern its implementation details.

## Scope and ownership

- Resolve repositories through `repositories.json`; paths are relative to this file.
- State which repositories a task will modify. Read dependencies as needed; avoid
  unrelated changes. Use `git -C <repo>` and explicit command working directories.
- Inspect branch and working-tree state first. Preserve existing user changes.
- Each application owns its dependencies, lockfile, environment, build output,
  migrations, and commits. Never copy these between repositories.
- Root Git tracks only the allowlisted coordination files. Do not force-add an
  application directory, nested `.git`, credentials, database dumps, or uploads.
- Git branches are not file isolation. Concurrent writers to one repository need
  separate worktrees. A cross-repository task needs one worktree per affected repo.
- Record the actual checkout paths for a worktree task; sibling paths may no longer
  resolve. If this workspace is absent, use local repository guidance and obtain
  the shared context before changing cross-repository contracts.

## Data and integration

- Laravel owns platform business data; Payload owns marketing content. Do not
  infer synchronization or shared authentication from similar entity names.
- Keep API routes, response envelopes, enum values, roles, and WebSocket channels
  compatible with all consumers. Update producer, affected consumers, and docs
  together; link their separate commits/PRs using one work item ID.
- Ordinary frontend builds must not migrate, seed, rewrite, or publish CMS data.
- Inspect the target environment before any migration or maintenance script.
  Use disposable test databases for destructive tests; never reset a shared DB.
- Worktrees do not isolate databases, uploads, queues, or ports. Configure those
  separately when running concurrent integration environments.

## Completion

- Run checks appropriate to changes plus required repository checks. Report
  failures, unavailable runtimes, and untested integration paths accurately.
- Review each repository's diff independently and stage explicit files.
- Record material findings and remaining work in `WORK_ITEMS.md`; update shared
  architecture only when an implementation or decision actually changes.
- Commit and publish to the intended remote only. The workspace remote is owned
  by `farnai`; the application remotes currently point to `sarsevanidze`.
- No deployment, CMS publication, or database migration is implied by a code push.
