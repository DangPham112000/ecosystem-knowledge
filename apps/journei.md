# Journei (Journey Planner)

- Repo: [DangPham112000/Journei](https://github.com/DangPham112000/Journei). URL: `https://journei.dantepham.site` (`DOMAIN` in Doppler).
- Stack: pnpm workspace; backend Express + Apollo GraphQL + MongoDB; frontend React + Vite + shadcn (Base UI). Google Maps (`VITE_GOOGLE_MAPS_API_KEY`). MCP server at `POST /mcp` (Clerk OAuth).
- Sign-in: code moved to Clerk (merged 2026-10-11) and links existing users by email on first sign-in. **Live on the shared Clerk app since 2026-10-24 (DAN-85)**; SSO with `accounts.dantepham.site` verified. The backend only accepts tokens from its own origin (`CLERK_AUTHORIZED_PARTIES=https://${DOMAIN}`). No real users yet (2026-10-24).
- Data: `Plan` belongs to a user (`userId` = Clerk id), `Place` belongs to a `Plan`, `ScheduledActivity` references a `Place`. Google Calendar is not implemented; `Plan.googleEventId` is unused.
- Users (DAN-93, Journei #53, 2026-10-26): **no own user table**. Email, name and avatar come from `core.users` (Clerk API fallback); GraphQL `User.id` is the Clerk id and has `avatar` (was `picture`). The old `users` collection was dropped on startup once every plan pointed at a Clerk id; a dump from before is in `backups/pre-dan-93.archive` in the deploy directory.
- Deploy: VPS directory `/opt/journei` (DAN-107), compose project name pinned to `journei`. No `.env` on the VPS.
- Doppler: project `journei/prd` holds `CLERK_SECRET_KEY` and `CLERK_PUBLISHABLE_KEY` (both `${shared.prd.*}`), `VITE_GOOGLE_MAPS_API_KEY`, `DOMAIN`. The frontend build reads `CLERK_PUBLISHABLE_KEY` and passes it as `VITE_CLERK_PUBLISHABLE_KEY` (Journei PR #49).
- Next: DAN-94, DAN-95, DAN-108.
- MongoDB: database `journey-planner` on the shared `ecosystem-mongo` (user `journei`, DAN-90, Journei #51), started empty. `clear-mongodb.yml` drops its collections there. See [infra/mongo.md](../infra/mongo.md).
