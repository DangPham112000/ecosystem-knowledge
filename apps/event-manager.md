# Event Manager

- Repo: [DangPham112000/EventManager](https://github.com/DangPham112000/EventManager). URL: `https://events.dantepham.site`.
- Stack: pnpm workspace, TypeScript; backend Express + Apollo GraphQL + MongoDB/Mongoose; frontend React + Vite; Docker Compose + nginx.
- Sign-in: the shared Clerk app `yolo-ecosystem` since 2026-10-25 (DAN-100, PR #27). The backend only accepts session tokens from its own origin (`CLERK_AUTHORIZED_PARTIES=https://events.dantepham.site`). MCP: Clerk OAuth or personal `emk_` API keys.
- Has `find_conflicts` (overlap check), useful for the Journei ↔ Event Manager flow.
- Secrets: Doppler project `event-manager`, deployed through Doppler since PR #26 (2026-10-23). VPS directory `/opt/event-manager`.
- Users (DAN-92, PRs #30 and #31, 2026-10-25): **no own user table**. Events, attendees and API keys reference users by `clerkId`; email, name and avatar are read from `core.users` (Clerk API fallback if the webhook has not arrived yet). GraphQL `User.id` is the Clerk id. App-only settings (calendar share) live in `usersettings { clerkId, ... }`. The old `users` collection was dropped; a dump from before the change is in `backups/pre-dan-92.archive` in the deploy directory.
- Next: remove mock mode (DAN-108), `/privacy` page (DAN-109).
- MongoDB: database `event-manager` on the shared `ecosystem-mongo` (user `event-manager`, DAN-90, EventManager #29); data moved by `scripts/migrate-to-shared-mongo.sh`. See [infra/mongo.md](../infra/mongo.md).
