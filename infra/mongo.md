# Shared MongoDB

_DAN-90, 2026-10-25. Code: private repo [DangPham112000/ecosystem-infra](https://github.com/DangPham112000/ecosystem-infra), directory `mongo/`._

One `mongo:7` container, `ecosystem-mongo`, with authentication on, deployed to `/opt/ecosystem-mongo`. Each app keeps **its own database** and **its own Mongo user**; there is no single shared database for app data (decided 2026-10-25, see [decisions.md](../decisions.md)).

| App | Mongo user | Database | Password (Doppler `shared/prd`) |
| --- | --- | --- | --- |
| Event Manager | `event-manager` | `event-manager` | `MONGO_PASSWORD_EVENT_MANAGER` |
| Journei | `journei` | `journey-planner` | `MONGO_PASSWORD_JOURNEI` |
| News | `news` | `news` | `MONGO_PASSWORD_NEWS` |
| Bill Splitter | `bill-splitter` | `bill-splitter` | `MONGO_PASSWORD_BILL_SPLITTER` |
| core-users service | `core` | `core` (only writer) | `MONGO_PASSWORD_CORE` |

- Every app user has `readWrite` on its own database and `read` on `core` (for `core.users`, DAN-91, see [core-users.md](core-users.md)). Writing to another app's database, or to `core`, is refused.
- The stack creates the Docker network `ecosystem`. App backends join it as an external network and connect with `mongodb://<user>:${MONGO_PASSWORD}@ecosystem-mongo:27017/<database>?authSource=admin`. No port is published on the host.
- Each app's Doppler `prd` has `MONGO_PASSWORD = ${shared.prd.MONGO_PASSWORD_<APP>}`. App deploys stop before changing anything if the `ecosystem` network or `MONGO_PASSWORD` is missing, so **deploy the Mongo stack first**.
- Deploy: pushing `mongo/**` or `core/**` to `main` of `ecosystem-infra` (or running "Deploy shared infra" by hand). The workflow runs `doppler run -- docker compose up -d --wait`, then `ensure-users.js`, which creates missing users and resets passwords and roles. Doppler project `ecosystem-infra/prd` only references `shared/prd` (`MONGO_*`, `VPS_*`).
- Changing an app password: change it in `shared/prd`, redeploy the Mongo stack, then redeploy the app. `MONGO_ROOT_PASSWORD` is only read on the first start; changing it later also needs `db.changeUserPassword` inside Mongo.
- WiredTiger cache capped at 0.5 GB.
- A new app: add a row to `ensure-users.js`, a `MONGO_PASSWORD_<APP>` in `shared/prd` and in `ecosystem-infra/prd`, then `MONGO_PASSWORD` in the app's project.
- Not done yet: scheduled backups of the shared instance.

## Move from per-app containers (2026-10-25)

Only Event Manager's data was kept (Dang: production data is only his own, in Event Manager). Its deploy ran `scripts/migrate-to-shared-mongo.sh` once: dump to `/opt/event-manager/backups/pre-dan-90.archive`, restore, compare counts. Bill Splitter, News and Journei started on empty databases and their old volumes were deleted; News API keys (`nwk_`) had to be created again.
