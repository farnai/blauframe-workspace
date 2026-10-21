# Blauframe workspace

საერთო სამუშაო სივრცე Blauframe-ის ოთხი დამოუკიდებელი პროექტისთვის.
აქ ინახება არქიტექტურა, მუშაობის წესები და პროექტებს შორის შეთანხმებები.
თითოეული აპლიკაციის კოდი და Git ისტორია საკუთარ რეპოზიტორიაში რჩება.

## Start here

- [SYSTEM.md](SYSTEM.md): boundaries, data ownership, integrations, source links.
- [DEVELOPMENT.md](DEVELOPMENT.md): setup, ports, checks, branches and releases.
- [AGENTS.md](AGENTS.md): shared working instructions for coding agents.
- [WORK_ITEMS.md](WORK_ITEMS.md): findings, implementation status and next work.
- [repositories.json](repositories.json): repository locations and audit baseline.

## Directory layout

```text
workspace/                       # this repository (currently C:\Projects)
  .git/
  AGENTS.md
  SYSTEM.md
  DEVELOPMENT.md
  blauframe-admin/                # independent .git, ignored by workspace Git
  blauframe-api/                  # independent .git, ignored by workspace Git
  blauframe-app/                  # independent .git, ignored by workspace Git
  blauframe-frontend/             # independent .git, ignored by workspace Git
```

There are no Git submodules and no shared package installation. Cloning this
repository does not clone the applications. On a new machine, clone this repository
first, then clone each URL in `repositories.json` into its listed relative path.
Existing checkouts do not need to be moved or reinitialized.

Open this directory for coordinated work, or open `blauframe.code-workspace` in an
editor that supports multi-root workspaces. A chat opened inside one application
must explicitly read the shared instructions: nested Git roots may stop automatic
discovery at the application boundary.

Root `.gitignore` uses an allowlist. Application changes never appear in the root
commit; inspect and commit them in their own repository. Adding a new shared file
requires intentionally adding its path to the allowlist.

Shared technical documentation is in English to match the existing repository
documentation. The initial implementation is tracked under **BF-WORKSPACE-001**.
