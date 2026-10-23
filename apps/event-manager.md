# Event Manager

- Repo: [DangPham112000/EventManager](https://github.com/DangPham112000/EventManager). URL: `https://events.dantepham.site`.
- Stack: pnpm workspace, TypeScript; backend Express + Apollo GraphQL + MongoDB/Mongoose; frontend React + Vite; Docker Compose + nginx.
- Sign-in: its own Clerk app, Development instance used in production too (by choice). `User { email, name, clerkId, googleId, avatar }`; on first sign-in a user is linked to an existing user by email (`backend/src/auth.ts`). Clerk OAuth for MCP/AI agents.
- Has `find_conflicts` (overlap check), useful for the Journei ↔ Event Manager flow.
- Secrets: Doppler project `event-manager`, deployed through Doppler since PR #26 (2026-10-23). VPS directory `/opt/event-manager`.
- Next: move to the shared Clerk app (DAN-100), read `core.users` (DAN-92), remove mock mode (DAN-108).
