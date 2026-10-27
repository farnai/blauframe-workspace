# System map

Baseline inspected on 2026-10-21. Source inspection does not establish production
configuration or prove an end-to-end flow works. Versions below come from manifests.

| Repository | Stack | Owns | Main integration |
| --- | --- | --- | --- |
| `blauframe-api` | Laravel 12, PHP, Sanctum, Reverb | Business rules, tenant data, roles, billing, onboarding, appointments, messaging | `/api/v1/cp`, `/api/v1/app` |
| `blauframe-admin` | Next 14.2.29, React 18, Tailwind 3 | Business administration UI | Laravel CP API; also defines an App API client |
| `blauframe-app` | Next 14.2.35, React 18, Tailwind 3 | Customer workspace UI | Laravel App API |
| `blauframe-frontend` | Next 16.2.3, React 19, Tailwind 4, Payload 3 | Marketing pages, marketing posts, display pricing, CMS accounts/media | Payload local API and PostgreSQL |

```text
Business admin ---- /api/v1/cp ----+
                                 +--> Laravel --> business database / media
Client portal ---- /api/v1/app ---+       |
       |                                +--> queue worker + Reverb
       +---------- WebSockets --------------------------+

Marketing pages <--> embedded Payload CMS <--> marketing PostgreSQL / media
```

## Administration and authentication

- Business administration is the `blauframe-admin` application. Its layout admits
  `super_admin` only. Laravel's `admin` middleware admits `admin` and `super_admin`;
  other middleware/permissions may restrict individual endpoints further.
- Marketing administration is `/admin` inside `blauframe-frontend`. Payload has
  its own `users` collection and authentication. There is no verified shared login
  or token exchange with Laravel.
- Laravel tenant scoping and server permissions remain authoritative. Frontend
  guards are not a substitute for backend enforcement.

## Data ownership

| Data | Authoritative system | Consumers / editing surface |
| --- | --- | --- |
| Companies, client users, projects, tasks, onboarding, appointments | Laravel | Business Admin and Client Portal |
| Subscription and transaction records | Laravel | Business Admin and Client Portal |
| Platform blogs, FAQs, guides, service catalog | Laravel | Business Admin editing; Client Portal consumption |
| Marketing page blocks, marketing posts and pricing display | Payload | Payload admin and public marketing pages |
| CMS users and marketing uploads | Payload | Payload admin; public media where configured |

Marketing pricing is not automatically synchronized with billable subscription
plans. Marketing and platform blog records are also separate. A future shared
catalog requires an explicit owner, ID mapping, synchronization direction and
failure behavior; do not introduce bidirectional copying by assumption.

## Contract sources

- Routes: `blauframe-api/routes/api.php` and `routes/resources/{cp,application}`.
- Response shape: actual Resource/Controller code and `app/Traits/HttpResponse.php`.
- OpenAPI: existing annotations and `storage/api-docs/api-docs.json` in the API
  repository. Regenerate and validate coverage before using it for generated clients.
- Frontend HTTP clients: each portal's `src/services/api.ts`.
- Real-time: API `routes/channels.php`, events, and each portal's `src/lib/echo.ts`.
  Use channel `Conversation.{id}` and event `.MessageSent` for conversation chat;
  user notification channel is `App.Models.User.{id}`. Echo supplies `private-`.
- Locales: both portal dictionaries and Payload config currently use `en` / `de`.

## Known integration boundaries

- Landing booking reads available slots from Laravel's public
  `/api/v1/public/onboarding/availability` endpoint and redirects the selected UTC
  slot to the Client Portal registration/login flow. The portal preserves the slot
  and schedules onboarding stage 1 through Laravel after authentication. This is
  the source-level contract; production end-to-end persistence still needs a
  controlled test account and isolated test data.
- Landing's localized login route redirects to the Client Portal `/login` using
  `getClientPortalBaseUrl()`. The production destination still depends on runtime
  configuration and was not confirmed against the live site. CMS-controlled links
  may redirect elsewhere; their live values were not inspected.
- Both portal HTTP clients normalize `NEXT_PUBLIC_API_URL` to include `/api` once.
  Keep the configured value ending in `/api` without a trailing slash because
  Echo's broadcast-auth URLs append `/v1/{app|cp}/broadcasting/auth` directly.
- No root integration test pipeline was present at the initial audit. Existing
  repository tests do not prove all cross-repository contracts work together.
- Echo clients target portal-specific broadcast-auth URLs. Laravel registers
  Sanctum-protected routes at `/api/v1/app/broadcasting/auth` and
  `/api/v1/cp/broadcasting/auth`, and allows the broadcasting path through CORS.
  Source and regression-test evidence do not establish that Forge has the API
  revision deployed or that production Reverb is healthy.

## Documentation map

The following paths resolve locally once the application repositories are cloned;
they are deliberately not stored in this repository.

- Admin: `blauframe-admin/README.md`, `AGENTS.md`, `docs/`.
- API: `blauframe-api/README.md`, `AGENTS.md`, `docs/`.
- App: `blauframe-app/README.md`, `AGENTS.md`, `docs/`.
- Landing: `blauframe-frontend/README.md`, `INSTALL.md`, `DEPLOYMENT.md`, `AGENTS.md`.

Keep detailed implementation documentation with its owning code. Update this map
when ownership or a cross-repository contract changes.
