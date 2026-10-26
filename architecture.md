# Target architecture

Based on the feasibility report of 2026-10-12, adjusted for the decision to create a new Clerk app. Not finished yet; progress is in [linear.md](linear.md).

```
       Clerk (one NEW application, production on dantepham.site)
                         │  session token (JWT)
   ┌──────────────┬──────┴───────┬──────────────┐
events.*      journei         bills          news
   │              │              │              │
   └──── verify the token with the same CLERK_SECRET_KEY ┘
                         │
          Shared MongoDB (ecosystem-mongo, DAN-90)
          ├─ core.users      ← synced from the Clerk webhook (DAN-91)
          ├─ event-manager
          ├─ journey-planner
          ├─ bill-splitter
          └─ news
```

## Shared sign-in (SSO)

- A Clerk Production instance on the root domain `dantepham.site` sets the session cookie on the root, so signing in to one app signs you in on every subdomain. No satellite domains (a paid feature) are needed as long as every app lives under `*.dantepham.site`.
- Every backend verifies tokens with the same `CLERK_SECRET_KEY`; `CLERK_AUTHORIZED_PARTIES` lists all subdomains.
- Google sign-in stays available by enabling Google in Clerk.
- Existing users are linked by email on their first sign-in (Event Manager and Journei already do this in `backend/src/auth.ts`).

## Shared users

- A `core.users` collection (email, name, avatar, clerkId), updated by the `user.created / user.updated / user.deleted` webhooks through the core-users service, its only writer (DAN-91, [infra/core-users.md](infra/core-users.md)).
- Each app's business data references users by `clerkId`.
- Requires consolidating MongoDB into one instance (each app keeps its own database); this also saves RAM on the VPS.

## Data sharing

App A's backend calls app B's GraphQL API with the user's own Clerk session token, over the `ecosystem` network; A's origin must be in B's `CLERK_AUTHORIZED_PARTIES` (see decisions.md, 2026-10-26). Flows (DAN-94..98):

- Journei → Bill Splitter: create a splitter for a trip with the travellers already invited (DAN-94, waits for Bill Splitter's DAN-35).
- Journei ↔ Event Manager: itinerary activities appear as calendar events, with overlap checks (DAN-95, built 2026-10-26).
- Event Manager → Bill Splitter: a shared fund for an event.
- A shared "people I often go with" contact list.
- News → Journei / Event Manager: news about a trip's destination or an event's topic.

## Risks

- Clerk is a single point of failure for every app.
- Clerk's free-plan limits count across the whole ecosystem (the exact numbers are not verified).
- An app outside `*.dantepham.site` would need satellite domains (paid).
- Reading each other's databases creates coupling; only `core.users` is shared.
