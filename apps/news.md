# News

- Repo: [DangPham112000/news](https://github.com/DangPham112000/news). VPS directory `/opt/news`.
- Now the same stack as Event Manager: pnpm workspace, Express 5 + Apollo GraphQL + MongoDB/Mongoose; frontend React 19 + Vite + Tailwind + Redux Toolkit. (The 2026-10-12 report describes the M1 version on REST + SQLite; that was replaced around 2026-10-14.)
- AI agents push news per topic through MCP (`POST /mcp`: `list_topics`, `list_news`, `upsert_news`) and the `upsertArticles` mutation; both require an API key `Authorization: Bearer nwk_...` (created with `pnpm --filter backend apikey create <name>`).
- No users and no Clerk yet. When per-user features arrive: store `clerkId` and verify Clerk tokens (DAN-86).
- Secrets: Doppler project `news` (`RATE_LIMIT_PER_MIN`, ...).
- Next: DAN-86 (DAN-83 closed 2026-10-24: writes already need an API key), DAN-108.
