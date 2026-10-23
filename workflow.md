# Working style

_Decided on 2026-10-22._

- **Production only.** No dev or staging environment. Push to `main` → GitHub Actions deploys → verify on the live subdomain.
- **Mock mode will be removed** from Event Manager, Journei and News (DAN-108). Do not propose mock-based testing or local run setups.
- **Dev machines hold no secrets**: no `.env`, no `doppler.yaml`. The Doppler CLI on a dev machine is only used to add or edit secrets.
- Dang uses Windows/PowerShell; command instructions should include a PowerShell version.
- Chat with Dang in Vietnamese; files in this repo are in English (see [CONTRIBUTING.md](CONTRIBUTING.md)).
- Pushing to an app's `main` **deploys to production**, so PRs need Dang's review before merging.
