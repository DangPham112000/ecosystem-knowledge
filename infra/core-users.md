# Shared users: core-users

_DAN-91, 2026-10-25. Code: private repo [DangPham112000/ecosystem-infra](https://github.com/DangPham112000/ecosystem-infra), directory `core/`._

`core.users` is the one user table shared by every app. It is filled by the **core-users** service from the shared Clerk app `yolo-ecosystem`.

```
Clerk ──webhook──▶ core.dantepham.site/webhooks/clerk ─▶ core-users ─▶ ecosystem-mongo / core.users
                                                                        ▲ read-only
                                         Event Manager, Journei, News, Bill Splitter
```

## Document

| Field | Meaning |
| --- | --- |
| `clerkId` | Clerk user id (`user_...`), unique. Business data references users by this. |
| `email` | Primary email, lowercased, indexed. |
| `name` | "First Last", else the username, else `null`. |
| `avatar` | Clerk image URL. |
| `clerkCreatedAt`, `clerkUpdatedAt` | Timestamps from Clerk. |
| `createdAt`, `syncedAt` | When core-users first stored and last wrote the user. |

## Rules

- Only core-users writes `core` (Mongo user `core`). Every app user has `read` on `core`; reads go through the app's own Mongo connection (`client.db('core').collection('users')`).
- Webhook events: `user.created`, `user.updated`, `user.deleted`, verified with the Svix signature. Late (older) updates are ignored.
- A full sync from the Clerk API runs on every deploy of `ecosystem-infra` (backfill, and repair after missed webhooks). To force one, rerun the "Deploy shared infra" workflow.
- Readers (2026-10-26): Event Manager (DAN-92), Journei and Bill Splitter (DAN-93). No app has its own user table any more. Lookup by email is internal only in each app, not exposed by any API.
- Only identity lives here. Anything app-specific (preferences, roles) stays in the app's own database, keyed by `clerkId`.

## Deploy

- `/opt/core-users`, host port `127.0.0.1:8084`, host nginx serves `core.dantepham.site`.
- Secrets in Doppler `ecosystem-infra/prd`: `MONGO_PASSWORD_CORE` (references `shared/prd`), `CLERK_SECRET_KEY` (references `shared/prd`), `CLERK_WEBHOOK_SIGNING_SECRET` (the Clerk endpoint's signing secret, stored only here).
- Deployed by the same workflow as the Mongo stack, after it.
