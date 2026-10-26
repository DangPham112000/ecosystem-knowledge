# Decision log

Newest first. Each entry: date, decision, reason.

## 2026-10-26: Every app reads users from `core.users`; no app has its own user table

DAN-93 (Journei #53, bill-splitter #6). Journei dropped its `users` collection: plans reference their owner by `clerkId`, moved on startup by email match in `core.users`, with a one-time dump in `backups/pre-dan-93.archive`. Bill Splitter never had a user table and now reads email, name and avatar from `core.users` too. Both use the same Clerk API fallback as Event Manager. Looking people up by email in `core.users` (for invites) exists only as an internal helper in each app: it is not exposed through any API until an invite feature needs it, because an open email lookup lets anyone probe who has an account.

## 2026-10-25: Event Manager has no user table; it reads `core.users`

DAN-92 (EventManager PRs #30, #31). Users are referenced by `clerkId` everywhere in Event Manager's database; identity comes from `core.users`, read-only. App-specific per-user data stays in the app's own database keyed by `clerkId` (Event Manager: `usersettings`). If `core.users` does not have a user yet (webhook delay on first sign-up), the app asks the Clerk API instead and never writes `core`. This is the pattern for Journei and Bill Splitter (DAN-93).

## 2026-10-25: `core.users` is written only by a small core-users service

DAN-91 (ecosystem-infra PR #2). Dang chose a dedicated service over putting the webhook in Event Manager's backend, so no app needs write access to `core` and user sync does not depend on any one app being up. The service lives in `ecosystem-infra` (`core/`), receives Clerk webhooks at `https://core.dantepham.site/webhooks/clerk`, and is the only Mongo user (`core`) that can write the `core` database. A full sync from the Clerk API runs on every deploy, which is both the backfill and the fix for missed webhooks. Apps read `core.users` by `clerkId` (DAN-92, DAN-93). Details in [infra/core-users.md](infra/core-users.md).

## 2026-10-25: One shared MongoDB instance, one database per app

DAN-90. Dang asked whether to normalize everything into a single database; the answer was no. One instance (`ecosystem-mongo`, repo `ecosystem-infra`, `/opt/ecosystem-mongo`) with authentication, but each app keeps its own database and its own Mongo user that can only write that database (plus read `core`). Reasons: a bug in one app cannot touch another app's data, schemas change independently, and backups and removals stay per app. Only user identity is shared (`core.users`, DAN-91); everything else still goes through APIs. Infrastructure code lives in the new private repo `ecosystem-infra`, not in this public repo. Only Event Manager's data was migrated. Details in [infra/mongo.md](infra/mongo.md).

## 2026-10-25: News is live with optional sign-in on the shared Clerk app

DAN-86 done (news PRs #5 and #6). Dang chose optional sign-in: the feed stays public, and sign-in only identifies the person (`me { clerkId }`) for future per-person features. No user table. First deploy of News: `news.dantepham.site`, `/opt/news`, host port 8082. Host ports on the VPS: Event Manager 8080, Journei 8081, News 8082, Bill Splitter 8083.

## 2026-10-25: GitHub keeps only DOPPLER_TOKEN; deploy secrets live in Doppler

GitHub never shows a secret's value again, so Dang could not copy deploy secrets from one repo to another. Every app's workflows now read `VPS_HOST`, `VPS_USERNAME` and `VPS_SSH_KEY` from Doppler (one copy in `shared/prd`, referenced by each app's `prd`), masked in the logs. GHCR push and the VPS pull use the job's own `GITHUB_TOKEN`, so the `GH_PAT` / `GHCR_PAT` personal access tokens go away. A new deploy SSH key replaces the old ones. PRs: EventManager #28, Journei #50, news #5, bill-splitter #4.

## 2026-10-25: Event Manager is on the shared Clerk app

DAN-100 done (EventManager PR #27). Existing users were relinked by email on their first sign-in, so no user import was needed. Doppler `event-manager/prd` references `${shared.prd.CLERK_*}` and keeps its own `CLERK_AUTHORIZED_PARTIES`, so session tokens from other apps are refused. MCP OAuth needs CIMD and DCR turned on in Clerk (see [infra/clerk.md](infra/clerk.md)); OAuth connectors had to reconnect, API keys kept working. Both apps with users (Event Manager, Journei) now share one Clerk app and one session.

## 2026-10-24: Shared Clerk app `yolo-ecosystem` is live; only production keys are stored

DAN-82 done. Production instance on `dantepham.site` with the allowed-subdomains list on. Only production keys go into Doppler `shared/prd`, because there is no dev environment. A new app's subdomain must be added to Clerk's allowed subdomains and to `CLERK_AUTHORIZED_PARTIES`. Details in [infra/clerk.md](infra/clerk.md).

## 2026-10-23: This repo is written in English

Files, commits and PRs in this repo use English; chat with Dang stays in Vietnamese.

## 2026-10-23: The `ecosystem-knowledge` repo is how projects share knowledge

Each Claude project has its own memory and does not sync with the others. This repo is added to every project (Ecosystem, Event Manager, Journei), and each project's instructions say to read it before working and to record decisions that affect other apps. Work items stay in Linear.

## 2026-10-22: Production is the only environment

Mock mode will be removed (DAN-108). No dev or staging environment and no `.env` on dev machines; changes are verified on the live site after deploy. Doppler keeps only the `prd` environment. See [workflow.md](workflow.md).

## 2026-10-22: Keep the Doppler layout with cross-project references

Dang verified that `${shared.prd.X}` works on the free plan, so the design stays: a `shared` project plus one project per app. Accepted risk: Doppler may block this feature (its docs list it as paid). The fallback is in [infra/doppler.md](infra/doppler.md).

## 2026-10-22: Journei uses the shared Clerk app, not its own

Journei's code already uses Clerk but has no Clerk app or keys. Journei waits for the shared Clerk app (DAN-82) and reads its keys from the Doppler `shared` project.

## 2026-10-22: Event Manager uses Clerk's Development instance in production

Deliberate choice (`pk_test_` / `sk_test_`); no Production instance is planned for Event Manager's old Clerk app. Event Manager will move to the shared Clerk app (DAN-100).

## 2026-10-12: Create a NEW Clerk application for the whole ecosystem

Replaces the original proposal (reuse Event Manager's Clerk app) to avoid confusion later. Its Production instance is tied to the root domain `dantepham.site` so SSO works across all subdomains. Event Manager must migrate too (DAN-100).

## 2026-10-12: Centralize secrets in Doppler

Done before Clerk (DAN-99 blocks DAN-82). DAN-99 was completed on 2026-10-22.

## 2026-10-12: Shared architecture

- The Clerk user ID is the shared identity; no app manages sign-in on its own.
- One thin user table, `core.users`, synced by a Clerk webhook, replaces each app's own User table.
- Data is shared through APIs (the other app's GraphQL, called with the user's Clerk token), never by reading another app's database. Only `core.users` is shared data.
- Cross-app links are stored as `{ app, id }` references, not copies.

Source: the feasibility report of 2026-10-12. Details in [architecture.md](architecture.md).
