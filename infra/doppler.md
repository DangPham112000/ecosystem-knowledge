# Doppler

_DAN-99 completed on 2026-10-22. Step-by-step guide (Vietnamese): [Hướng dẫn setup Doppler (DAN-99)](https://claude.ai/artifact/5Nku2RXAowyjbveJGygo7Y)._

## Layout

| Project | Config | Contents |
| --- | --- | --- |
| `shared` | `prd` | `CLERK_PUBLISHABLE_KEY`, `CLERK_SECRET_KEY`, `CLERK_AUTHORIZED_PARTIES` (production keys, added 2026-10-24 in DAN-82); apps reference them |
| `event-manager` | `prd` | App secrets + references to `shared` |
| `journei` | `prd` | App secrets + references to `shared` |
| `bill-splitter` | `prd` | App secrets (no production secrets yet) |
| `news` | `prd` | App secrets |

- Each project has **only the `prd` environment** (`dev` and `stg` were deleted on 2026-10-22).
- Cross-project reference: `${shared.prd.CLERK_SECRET_KEY}`. Same-config reference: `${CLERK_PUBLISHABLE_KEY}`.
- Free plan: 10 projects, 5 in use.

## Tokens and deploy

- Each app has two read-only service tokens on its `prd` config:
  - `vps-<app>`: set on the VPS, scoped to the app directory with `doppler configure set token --scope /opt/<app>`.
  - `github-actions-<app>`: stored as the GitHub secret `DOPPLER_TOKEN`, used to fetch `VITE_*` keys when building the frontend.
- Deploy on the VPS: `doppler run -- docker compose up -d --remove-orphans`. `docker-compose.yml` needs no changes.
- Changing a secret: re-run the deploy workflow. Changing the publishable key: the frontend must be rebuilt.
- Deploy secrets (`VPS_HOST`, `VPS_SSH_KEY`, `GH_PAT`, `GHCR_PAT`) stay in GitHub Secrets.
- In production `MONGODB_URI` is an internal Docker hostname, not a secret, so it stays in `docker-compose.yml` until DAN-90.

## Status per app

- Event Manager: deploys through Doppler (EventManager PR #26), token scoped to `/opt/event-manager`.
- Journei: not yet. Its Doppler steps are in a comment on DAN-82, to be done in DAN-85 after DAN-107 (move the deploy directory to `/opt/journei`).
- News, Bill Splitter: done when they get Clerk (DAN-86, DAN-84).

## Risk

Doppler's docs list cross-project references as a paid feature, but they currently work on the free plan. If Doppler blocks them: move to a single project with branch configs inheriting from the root config, or copy the Clerk keys into each app.

The git history of all four app repos was scanned on 2026-10-22: no leaked keys.
