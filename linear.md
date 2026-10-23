# Linear

Team `DangPhamWorkspace` (key `DAN`). Project: [Ecosystem: đăng nhập và dữ liệu dùng chung](https://linear.app/dangphamworkspace/project/ecosystem-djang-nhap-va-du-lieu-dung-chung-e53aa7c5cd47).

When ecosystem work starts or finishes, update the matching DAN issue instead of creating a new one. Issues are linked with blocked-by relations.

## Milestones

1. Shared sign-in (SSO)
2. Shared user table
3. Data sharing between apps

## Key issues

| Issue | Content | Status (2026-10-23) |
| --- | --- | --- |
| DAN-99 | Doppler for secrets (blocks DAN-82) | Done |
| DAN-82 | Create the new ecosystem Clerk app, with a setup guide | Not started |
| DAN-100 | Event Manager moves to the new Clerk app | |
| DAN-83 | News: lock the write API (Urgent) | To re-check: News already requires an API key for `upsertArticles` and MCP |
| DAN-84 | Bill Splitter adds Clerk | |
| DAN-85 | Journei moves to Clerk (sub-issues DAN-87/88/89) | Code has Clerk, waiting on DAN-82 |
| DAN-86 | News verifies Clerk tokens | |
| DAN-90 | Consolidate MongoDB into one instance | |
| DAN-91 | `core.users` + Clerk webhook endpoint | |
| DAN-92 | Event Manager reads `core.users` | |
| DAN-93 | Journei and Bill Splitter use `core.users` | |
| DAN-94..98 | Data-sharing flows | |
| DAN-107 | Move Journei's deploy directory to `/opt/journei` (before Journei's Doppler step) | |
| DAN-108 | Remove mock mode (Event Manager, Journei, News) | |

An empty status means it has not been recorded here; check Linear for the exact state.
