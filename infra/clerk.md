# Clerk

_Updated 2026-10-23._

## Plan

One **new** Clerk application for the whole ecosystem (DAN-82, not created yet), with its Production instance on the root domain `dantepham.site`. Its keys go into the Doppler `shared` project (config `prd`), and apps reference them as `${shared.prd.CLERK_SECRET_KEY}` and `${shared.prd.CLERK_PUBLISHABLE_KEY}`.

The setup guide for the new app is in issue [DAN-82](https://linear.app/dangphamworkspace/issue/DAN-82/clerk-tao-clerk-application-moi-cho-he-sinh-thai).

## Current state per app

| App | Clerk |
| --- | --- |
| Event Manager | Its own (old) Clerk app, using only the **Development** instance (`pk_test_`/`sk_test_`) even in production, by choice. Clerk OAuth for MCP. Will move to the shared app (DAN-100). |
| Journei | Code uses Clerk (merged 2026-10-11) but **has no Clerk app or keys yet**. Waits for the shared app (DAN-82). Clerk OAuth for MCP. |
| Bill Splitter | No sign-in yet. `adminUserId` and `Member.userId` are strings waiting for a clerkId (DAN-84). |
| News | No Clerk yet. Writes use `nwk_...` API keys for agents/MCP. Clerk token verification is DAN-86. |

## Common environment variables

- Backend: `CLERK_SECRET_KEY`, `CLERK_PUBLISHABLE_KEY` (OAuth discovery for MCP), `CLERK_AUTHORIZED_PARTIES` (comma-separated origins).
- Frontend (build time): `VITE_CLERK_PUBLISHABLE_KEY`, set in Doppler as `${CLERK_PUBLISHABLE_KEY}`.
