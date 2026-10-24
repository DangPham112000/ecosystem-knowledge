# Decision log

Newest first. Each entry: date, decision, reason.

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
