# ecosystem-knowledge

Shared knowledge base for Dang Pham's app ecosystem (Event Manager, Journei, News, Bill Splitter). Every project (Claude project or app repo) reads this repo before starting work and writes back here when a decision affects another app. How to update: see [CONTRIBUTING.md](CONTRIBUTING.md).

_Last updated: 2026-10-25._

## At a glance

- Apps run on subdomains of `dantepham.site`, on one shared VPS, each with its own Docker Compose + nginx.
- Goal: **one shared Clerk application** for the whole ecosystem (`yolo-ecosystem`, created in DAN-82), **one thin shared user table** (`core.users`, synced by a Clerk webhook), and **data sharing through APIs**, never by reading another app's database.
- Secrets live in **Doppler**: a `shared` project plus one project per app, with a single `prd` environment.
- **No dev or staging environment**: changes are verified directly on production after deploy.
- Work is tracked in **Linear**, project "Ecosystem: đăng nhập và dữ liệu dùng chung" (DAN-82..DAN-109).

## Contents

| File | Content |
| --- | --- |
| [decisions.md](decisions.md) | Decision log, newest first |
| [architecture.md](architecture.md) | Target architecture: SSO, shared users, data sharing |
| [workflow.md](workflow.md) | Working style: production only, no mocks |
| [infra/clerk.md](infra/clerk.md) | Clerk: current state and plan |
| [infra/doppler.md](infra/doppler.md) | Doppler: project layout, tokens, deploy |
| [infra/vps-deploy.md](infra/vps-deploy.md) | VPS, deploy directories, GitHub Actions |
| [linear.md](linear.md) | Linear project, milestones, key issues |
| [apps/event-manager.md](apps/event-manager.md) | Event Manager |
| [apps/journei.md](apps/journei.md) | Journei (Journey Planner) |
| [apps/news.md](apps/news.md) | News |
| [apps/bill-splitter.md](apps/bill-splitter.md) | Bill Splitter |

## Repositories

| App | Repo | Subdomain |
| --- | --- | --- |
| Event Manager | [DangPham112000/EventManager](https://github.com/DangPham112000/EventManager) | `events.dantepham.site` |
| Journei | [DangPham112000/Journei](https://github.com/DangPham112000/Journei) | from the `DOMAIN` variable in Doppler |
| News | [DangPham112000/news](https://github.com/DangPham112000/news) | not recorded yet |
| Bill Splitter | [DangPham112000/bill-splitter](https://github.com/DangPham112000/bill-splitter) | `bills.dantepham.site` |
