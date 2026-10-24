# Clerk

_Updated 2026-10-24._

## Shared Clerk application

Created and set up in [DAN-82](https://linear.app/dangphamworkspace/issue/DAN-82/clerk-tao-clerk-application-moi-cho-he-sinh-thai) (done 2026-10-24).

- Clerk app **`yolo-ecosystem`**, Hobby (free) plan. Sign-in with Email and Google.
- Production instance on the root domain `dantepham.site`: Frontend API `clerk.dantepham.site`, Account Portal `accounts.dantepham.site`. The session cookie lives on the root domain, so every subdomain shares one session (no satellite domains, which are paid).
- DNS (Cloudflare): five CNAMEs from Clerk's Domains page (`clerk`, `accounts`, `clkmail`, `clk._domainkey`, `clk2._domainkey`). They **must stay "DNS only"** (not proxied), or verification and SSL break. Clerk issues SSL itself.
- Google sign-in uses custom OAuth credentials from the Google Cloud project `yolo-ecosystem` (External, published; only email/profile scopes, so no Google review). Its consent screen points to `https://events.dantepham.site` and `https://events.dantepham.site/privacy` (the page is DAN-109).
- **Allowed subdomains** (Clerk → Domains) is on: `events`, `journei`, `bills`, `news`, `accounts` under `dantepham.site`.
- Keys: only the production keys are stored, in Doppler `shared/prd` (`CLERK_PUBLISHABLE_KEY`, `CLERK_SECRET_KEY`, `CLERK_AUTHORIZED_PARTIES`). Apps reference them as `${shared.prd.CLERK_SECRET_KEY}` etc. Development keys are not stored, since there is no dev environment.
- Webhooks (`user.created/updated/deleted`) are added in DAN-91.
- Hobby limits (checked 2026-10-24): 50,000 monthly retained users per app, shared by the whole ecosystem; up to 3 social providers.

**Adding a new app or subdomain:** add it to Allowed subdomains in Clerk **and** to `CLERK_AUTHORIZED_PARTIES` in Doppler `shared/prd`, then redeploy the apps.

## Current state per app

| App | Clerk |
| --- | --- |
| Event Manager | Its own (old) Clerk app, using only the **Development** instance (`pk_test_`/`sk_test_`) even in production, by choice. Clerk OAuth for MCP. Will move to the shared app (DAN-100). |
| Journei | Code uses Clerk (merged 2026-10-11), not connected yet. Will be the first app on the shared app (DAN-85). Clerk OAuth for MCP. |
| Bill Splitter | No sign-in yet. `adminUserId` and `Member.userId` are strings waiting for a clerkId (DAN-84). |
| News | No Clerk yet. Writes use `nwk_...` API keys for agents/MCP. Clerk token verification is DAN-86. |

## Common environment variables

- Backend: `CLERK_SECRET_KEY`, `CLERK_PUBLISHABLE_KEY` (OAuth discovery for MCP), `CLERK_AUTHORIZED_PARTIES` (comma-separated origins).
- Frontend (build time): `VITE_CLERK_PUBLISHABLE_KEY`, set in Doppler as `${CLERK_PUBLISHABLE_KEY}`.
