# News

- Repo: [DangPham112000/news](https://github.com/DangPham112000/news). Live at `news.dantepham.site` since 2026-10-25 (first deploy). VPS directory `/opt/news`, frontend on host port 8082.
- Same stack as Event Manager: pnpm workspace, Express 5 + Apollo GraphQL + MongoDB/Mongoose; frontend React 19 + Vite + Tailwind + Redux Toolkit. (The 2026-10-12 report describes the M1 version on REST + SQLite; that was replaced around 2026-10-14.)
- AI agents push news per topic through MCP (`POST /mcp`: `list_topics`, `list_news`, `upsert_news`) and the `upsertArticles` mutation; both require an API key `Authorization: Bearer nwk_...`. On the VPS: `cd /opt/news && doppler run -- docker compose exec backend node backend/dist/cli/apikey.js create <name>` (also `list`, `revoke <id>`). claude.ai connectors cannot send that header; MCP over Clerk OAuth is not built yet.
- Sign-in: shared Clerk app, **optional** (DAN-86, news PR #5, 2026-10-25). The feed stays public. The backend verifies the session token and exposes `me { clerkId }`; there is no user table, per-person data will store `clerkId`.
- Secrets: Doppler `news/prd`: `CLERK_SECRET_KEY`, `CLERK_PUBLISHABLE_KEY` (refs to `shared`), `CLERK_AUTHORIZED_PARTIES=https://news.dantepham.site`, `VPS_*` (refs to `shared`). GitHub keeps only `DOPPLER_TOKEN`.
- Next: DAN-108 (remove mock mode).
- MongoDB: database `news` on the shared `ecosystem-mongo` (user `news`, DAN-90, news #7), started empty, so API keys were created again. See [infra/mongo.md](../infra/mongo.md).
