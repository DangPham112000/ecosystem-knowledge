# Clerk

_Updated 2026-10-25._

## Shared Clerk application

Created and set up in [DAN-82](https://linear.app/dangphamworkspace/issue/DAN-82/clerk-tao-clerk-application-moi-cho-he-sinh-thai) (done 2026-10-24).

- Clerk app **`yolo-ecosystem`**, Hobby (free) plan. Sign-in with Email and Google.
- Production instance on the root domain `dantepham.site`: Frontend API `clerk.dantepham.site`, Account Portal `accounts.dantepham.site`. The session cookie lives on the root domain, so every subdomain shares one session (no satellite domains, which are paid).
- DNS (Cloudflare): five CNAMEs from Clerk's Domains page (`clerk`, `accounts`, `clkmail`, `clk._domainkey`, `clk2._domainkey`). They **must stay "DNS only"** (not proxied), or verification and SSL break. Clerk issues SSL itself.
- Google sign-in uses custom OAuth credentials from the Google Cloud project `yolo-ecosystem` (External, published; only email/profile scopes, so no Google review). Its consent screen points to `https://events.dantepham.site` and `https://events.dantepham.site/privacy` (the page is DAN-109).
- **Allowed subdomains** (Clerk → Domains) is on: `events`, `journei`, `bills`, `news`, `accounts` under `dantepham.site`.
- Keys: only the production keys are stored, in Doppler `shared/prd` (`CLERK_PUBLISHABLE_KEY`, `CLERK_SECRET_KEY`, `CLERK_AUTHORIZED_PARTIES`). Apps reference them as `${shared.prd.CLERK_SECRET_KEY}` etc. Development keys are not stored, since there is no dev environment.
- OAuth applications → Settings (for MCP sign-in, enabled 2026-10-25): **Publish CIMD support** on with client admission "Any compatible CIMD client", **Publish DCR support** on (Clerk marks it deprecated, still needed for older clients), allowed and default scopes `email`, `profile`, `offline_access`.
- Webhooks: one endpoint, `https://core.dantepham.site/webhooks/clerk`, events `user.created/updated/deleted`, feeding `core.users` (DAN-91, see [core-users.md](core-users.md)). Its signing secret is `CLERK_WEBHOOK_SIGNING_SECRET` in Doppler `ecosystem-infra/prd`.
- Hobby limits (checked 2026-10-24): 50,000 monthly retained users per app, shared by the whole ecosystem; up to 3 social providers.

**Adding a new app or subdomain:** add it to Allowed subdomains (the `core` webhook subdomain is not a sign-in origin and needs neither) in Clerk **and** to `CLERK_AUTHORIZED_PARTIES` in Doppler `shared/prd`, then redeploy the apps. Every app's own `CLERK_AUTHORIZED_PARTIES` is `${shared.prd.CLERK_AUTHORIZED_PARTIES}` (since 2026-10-26), so all apps accept each other's session tokens; see decisions.md.

## Current state per app

| App | Clerk |
| --- | --- |
| Event Manager | On the shared app since 2026-10-25 (DAN-100, EventManager PR #27). Existing users were relinked by email on first sign-in. Clerk OAuth for MCP; connectors had to reconnect. The old Clerk app is no longer used and can be deleted. |
| Journei | On the shared app since 2026-10-24 (DAN-85), first app to use it. Clerk OAuth for MCP. |
| Bill Splitter | On the shared app since 2026-10-25 (DAN-84, bill-splitter PR #4), optional sign-in. Anonymous members still allowed; `clerkId` in the GraphQL context. |
| News | On the shared app since 2026-10-25 (DAN-86, news PR #5). Optional sign-in, feed public, no user table (`me { clerkId }`). Agents/MCP still use `nwk_...` API keys. |

## Common environment variables

- Backend: `CLERK_SECRET_KEY`, `CLERK_PUBLISHABLE_KEY` (OAuth discovery for MCP), `CLERK_AUTHORIZED_PARTIES` (comma-separated origins).
- Frontend (build time): `VITE_CLERK_PUBLISHABLE_KEY`, set in Doppler as `${CLERK_PUBLISHABLE_KEY}`.
