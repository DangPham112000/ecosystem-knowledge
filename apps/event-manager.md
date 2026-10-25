# Event Manager

- Repo: [DangPham112000/EventManager](https://github.com/DangPham112000/EventManager). URL: `https://events.dantepham.site`.
- Stack: pnpm workspace, TypeScript; backend Express + Apollo GraphQL + MongoDB/Mongoose; frontend React + Vite; Docker Compose + nginx.
- Sign-in: the shared Clerk app `yolo-ecosystem` since 2026-10-25 (DAN-100, PR #27). `User { email, name, clerkId, googleId, avatar }`; on sign-in with an unknown Clerk id the user is linked to the existing user by email and the stored `clerkId` is overwritten (`backend/src/auth.ts`). The backend only accepts session tokens from its own origin (`CLERK_AUTHORIZED_PARTIES=https://events.dantepham.site`). MCP: Clerk OAuth or personal `emk_` API keys.
- Has `find_conflicts` (overlap check), useful for the Journei ↔ Event Manager flow.
- Secrets: Doppler project `event-manager`, deployed through Doppler since PR #26 (2026-10-23). VPS directory `/opt/event-manager`.
- Next: read `core.users` (DAN-92), remove mock mode (DAN-108), `/privacy` page (DAN-109).
- MongoDB: database `event-manager` on the shared `ecosystem-mongo` (user `event-manager`, DAN-90, EventManager #29); data moved by `scripts/migrate-to-shared-mongo.sh`. See [infra/mongo.md](../infra/mongo.md).
