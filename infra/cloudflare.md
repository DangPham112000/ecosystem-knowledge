# Cloudflare

Cloudflare is the ecosystem's third platform, after GitHub and Google Cloud (decided 2026-11-20). Dang is on the **free plan**, which already includes a lot; check it when choosing a hosted service.

## In use

- DNS for `dantepham.site` and its subdomains.
- Proxy and TLS in front of the VPS (see [vps-deploy.md](vps-deploy.md)). Clerk's CNAMEs must stay "DNS only" (see [clerk.md](clerk.md)).

## Candidate uses (free plan)

Not set up yet. Check current free-plan limits before relying on them:

- **R2** object storage (free storage allowance, no egress fees): an alternative to Google Cloud Storage for MongoDB backups (DAN-64).
- **Workers** with Cron Triggers: small scheduled jobs and pings that run off the VPS, for example a second uptime probe.
- **Pages**: static sites such as the homepage.
- **Turnstile**: free CAPTCHA if a public form needs one.
- **Email Routing**: forwarding addresses on the domain.
- **Zero Trust / Access**: putting an admin page behind a login without app code.

## Choosing between Google Cloud and Cloudflare

Both are acceptable. Prefer the one where the service is free or clearly cheaper, and note the choice in [decisions.md](../decisions.md).
