# Journei (Journey Planner)

- Repo: [DangPham112000/Journei](https://github.com/DangPham112000/Journei). Domain comes from the `DOMAIN` variable.
- Stack: pnpm workspace; backend Express + Apollo GraphQL + MongoDB; frontend React + Vite + shadcn (Base UI). Google Maps (`VITE_GOOGLE_MAPS_API_KEY`). MCP server at `POST /mcp` (Clerk OAuth).
- Sign-in: code moved to Clerk (merged 2026-10-11) and links existing users by email on first sign-in. **No Clerk app or keys yet**; it will use the shared Clerk app (DAN-82), not its own.
- Data: `Plan` belongs to a `User` (`userId`), `Place` belongs to a `Plan`, `ScheduledActivity` references a `Place`. Google Calendar is not implemented; `User.refreshToken` and `Plan.googleEventId` are unused.
- Deploy: VPS directory `~/journei`, moving to `/opt/journei` (DAN-107). `~/journei/.env` still holds old Google OAuth/JWT variables (unused by the code); keep it until the Doppler deploy.
- Doppler: project `journei`; its steps are in a comment on DAN-82, to be done after DAN-107.
- Next: DAN-85 (sub-issues DAN-87/88/89), DAN-93, DAN-108.
