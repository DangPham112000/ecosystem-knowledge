# Using and updating this repo

## Language

**Everything in this repo is written in English**: files, commit messages and PR descriptions. Chat with Dang stays in Vietnamese. Proper names that are Vietnamese (such as the Linear project name) are kept as they are.

## When starting a task

1. Read [README.md](README.md), then the file for the app you are working on in `apps/`, and [decisions.md](decisions.md).
2. If the task touches sign-in, secrets or deployment, also read the matching file in `infra/`.

## When to write here

Record a change here when it **affects another app or shared infrastructure**, for example:

- Architecture decisions (Clerk, shared users, how data is shared, consolidating MongoDB).
- Changes to the Doppler layout, shared secret names, VPS deploy directories or subdomains.
- An app's status changing in a way other apps need to know (moved to the shared Clerk app, exposes a new API other apps can call).
- Changes to how Dang works (for example dropping mock mode).

Details that only matter inside one app (components, resolvers, internal bugs) belong in that app's repo, not here.

## How to write

- New decision: add an entry **at the top** of [decisions.md](decisions.md) with the date (YYYY-MM-DD), the decision, the reason and the Linear issue if any.
- Update the related files so they match the current state; do not leave contradicting old content. Delete superseded information or mark it "replaced by ... (date)".
- Update the "Last updated" line in the README.
- Keep files short. Always use absolute dates.
- **Never write secrets** (keys, tokens, passwords, connection strings with passwords). Write only the variable name and where it lives in Doppler.
- **This repo is public**: also leave out server IPs, SSH users and anything else that helps an attacker.
- Committing directly to `main` or opening a PR are both fine; the commit message should say which app changed.
