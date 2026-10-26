# Event Manager

- Repo: [DangPham112000/EventManager](https://github.com/DangPham112000/EventManager). URL: `https://events.dantepham.site`.
- Stack: pnpm workspace, TypeScript; backend Express + Apollo GraphQL + MongoDB/Mongoose; frontend React + Vite; Docker Compose + nginx.
- Sign-in: the shared Clerk app `yolo-ecosystem` since 2026-10-25 (DAN-100, PR #27). The backend accepts session tokens from every ecosystem app (`CLERK_AUTHORIZED_PARTIES=${shared.prd.CLERK_AUTHORIZED_PARTIES}`, 2026-10-26), but tokens from other apps cannot list, create or change API keys or the calendar share link (`requireOwnApp`, DAN-95). MCP: Clerk OAuth or personal `emk_` API keys.
- API for other apps (DAN-95, EventManager #32): GraphQL at `http://event-manager-backend:4000/graphql` on the `ecosystem` network; `createEvent` / `updateEvent` / `deleteEvent` and `conflicts(startTime, endTime, excludeEventId)` (same logic as the MCP `find_conflicts` / `check_availability` tools). Used by Journei's calendar sync.
- Secrets: Doppler project `event-manager`, deployed through Doppler since PR #26 (2026-10-23). VPS directory `/opt/event-manager`.
- Users (DAN-92, PRs #30 and #31, 2026-10-25): **no own user table**. Events, attendees and API keys reference users by `clerkId`; email, name and avatar are read from `core.users` (Clerk API fallback if the webhook has not arrived yet). GraphQL `User.id` is the Clerk id. App-only settings (calendar share) live in `usersettings { clerkId, ... }`. The old `users` collection was dropped; a dump from before the change is in `backups/pre-dan-92.archive` in the deploy directory.
- Next: remove mock mode (DAN-108), `/privacy` page (DAN-109).
- MongoDB: database `event-manager` on the shared `ecosystem-mongo` (user `event-manager`, DAN-90, EventManager #29); data moved by `scripts/migrate-to-shared-mongo.sh`. See [infra/mongo.md](../infra/mongo.md).
