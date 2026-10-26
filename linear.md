# Linear

Team `DangPhamWorkspace` (key `DAN`). Project: [Ecosystem: đăng nhập và dữ liệu dùng chung](https://linear.app/dangphamworkspace/project/ecosystem-djang-nhap-va-du-lieu-dung-chung-e53aa7c5cd47).

When ecosystem work starts or finishes, update the matching DAN issue instead of creating a new one. Issues are linked with blocked-by relations.

## Milestones

1. Shared sign-in (SSO)
2. Shared user table
3. Data sharing between apps

## Key issues

| Issue | Content | Status (2026-10-25) |
| --- | --- | --- |
| DAN-99 | Doppler for secrets (blocks DAN-82) | Done |
| DAN-82 | Create the new ecosystem Clerk app, with a setup guide | Done |
| DAN-100 | Event Manager moves to the new Clerk app | Done (2026-10-25, EventManager PR #27) |
| DAN-83 | News: lock the write API (Urgent) | Done (API keys for `upsertArticles` and MCP) |
| DAN-84 | Bill Splitter adds Clerk | Done (2026-10-25, bill-splitter #4) |
| DAN-85 | Journei moves to Clerk (sub-issues DAN-87/88/89) | Done (2026-10-24); DAN-87/88 Done, DAN-89 Canceled (no real users) |
| DAN-86 | News verifies Clerk tokens | Done (2026-10-25, news PRs #5, #6; optional sign-in) |
| DAN-90 | Consolidate MongoDB into one instance | Done (2026-10-25, ecosystem-infra #1, bill-splitter #5, news #7, Journei #51, EventManager #29) |
| DAN-91 | `core.users` + Clerk webhook endpoint | Done (2026-10-25, ecosystem-infra #2, #3; core-users service) |
| DAN-92 | Event Manager reads `core.users` | Done (2026-10-25, EventManager #30, #31; no own user table) |
| DAN-93 | Journei and Bill Splitter use `core.users` | Done (2026-10-26, Journei #53, bill-splitter #6; no app has its own user table) |
| DAN-94..98 | Data-sharing flows | |
| DAN-107 | Move Journei's deploy directory to `/opt/journei` (before Journei's Doppler step) | Done |
| DAN-108 | Remove mock mode (Event Manager, Journei, News) | |
| DAN-109 | Event Manager: public `/privacy` page (used by the Google OAuth consent screen) | |

An empty status means it has not been recorded here; check Linear for the exact state.
