# VPS and deploy

- One VPS shared by all apps, behind Cloudflare (TLS) and nginx. This repo is public, so **server IPs and SSH users are not recorded here**; see the `VPS_HOST` and `VPS_USERNAME` GitHub Secrets of each app repo.
- Per app: GitHub Actions builds Docker images, pushes them to GHCR, SSHes into the VPS, runs `docker compose pull`, then `doppler run -- docker compose up -d`.
- Pushing to an app repo's `main` = deploying to production.

| App | Directory on the VPS | Notes |
| --- | --- | --- |
| Event Manager | `/opt/event-manager` | Reads secrets from Doppler |
| Journei | `/opt/journei` | Reads secrets from Doppler (since 2026-10-24) |
| News | `/opt/news` | |
| Bill Splitter | not deployed | No deploy workflow yet |

- Each app currently runs its own `mongo:7` container; the plan is to consolidate into one instance (DAN-90).
- Journei: the `Cross-Origin-Opener-Policy: same-origin-allow-popups` header must match in `frontend/vite.config.ts`, `frontend/nginx.conf` and `nginx/nginx.conf`. New backend variables must be added to `environment:` in `docker-compose.yml`.
