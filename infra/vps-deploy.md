# VPS and deploy

- One VPS shared by all apps, behind Cloudflare (TLS) and nginx. This repo is public, so **server IPs and SSH users are not recorded here**; see the `VPS_HOST` and `VPS_USERNAME` GitHub Secrets of each app repo.
- Per app: GitHub Actions builds Docker images, pushes them to GHCR, SSHes into the VPS, runs `docker compose pull`, then `doppler run -- docker compose up -d`. The SSH host, user and key come from Doppler (`${shared.prd.VPS_*}`), not GitHub Secrets; GHCR uses the job's `GITHUB_TOKEN`.
- Pushing to an app repo's `main` = deploying to production.

| App | Directory on the VPS | Notes |
| --- | --- | --- |
| Event Manager | `/opt/event-manager` | Reads secrets from Doppler |
| Journei | `/opt/journei` | Reads secrets from Doppler (since 2026-10-24) |
| News | `/opt/news` | Reads secrets from Doppler; frontend on host port 8082 (since 2026-10-25) |
| Bill Splitter | `/opt/bill-splitter` | Reads secrets from Doppler; frontend on host port 8083 (since 2026-10-25) |
| Shared MongoDB | `/opt/ecosystem-mongo` | Repo `ecosystem-infra`; Doppler `ecosystem-infra/prd` (since 2026-10-25) |
| core-users | `/opt/core-users` | Repo `ecosystem-infra`; `core.dantepham.site`, host port 8084 (DAN-91) |

- MongoDB: one shared instance, `ecosystem-mongo` in `/opt/ecosystem-mongo` (repo `ecosystem-infra`, DAN-90). App stacks join its `ecosystem` network, so it must be deployed before them. See [mongo.md](mongo.md).
- Journei: the `Cross-Origin-Opener-Policy: same-origin-allow-popups` header must match in `frontend/vite.config.ts`, `frontend/nginx.conf` and `nginx/nginx.conf`. New backend variables must be added to `environment:` in `docker-compose.yml`.
