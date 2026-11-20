# Google Cloud

One Google Cloud project for the whole ecosystem: **`yolo-ecosystem`** (project number 320304558326), owned by Dang. Reuse it; don't create a project per app.

**Google is the ecosystem's second platform after GitHub** (decided 2026-11-20). Dang pays for the Google AI Pro plan, which comes with a small amount of Google Cloud credit every month. When an app needs a hosted service (monitoring, storage, scheduled jobs, AI), first check whether GitHub, Google Cloud in `yolo-ecosystem` or Cloudflare's free plan ([cloudflare.md](cloudflare.md)) covers it, before adding another third-party service.

| Use | Since | Details |
| --- | --- | --- |
| Google sign-in for the shared Clerk app | 2026-10-24 (DAN-82) | Custom OAuth credentials, consent screen External and published. See [clerk.md](clerk.md). |
| Uptime checks | 2026-11-20 (DAN-65) | Cloud Monitoring uptime check `journei-health`: HTTPS `https://journei.dantepham.site/health` (Journei #56) every 5 minutes from several regions, passes only on 2xx containing `"status":"ok"`. Alerting policy `Journei down` emails Dang after 5 minutes of failures. Runs outside the VPS, so it also catches the whole VPS going down. Other apps: add a `/health` endpoint and their own uptime check the same way. |
| Gemini API for Bill Splitter receipt OCR | 2026-11-12 (set up; code in DAN-116) | Gemini API (Generative Language API) enabled, Tier 1 (paid). Key `bill-splitter-receipt-ocr`, restricted to that API, stored as `GEMINI_API_KEY` in Doppler `bill-splitter/prd`. |

## Billing

- Paid from the monthly Google Cloud credits that come with Dang's Google AI Pro plan (Google Developer Program benefit). The billing account holding those credits must be linked to `yolo-ecosystem`.
- Gemini must run on the paid tier (billing linked), so user data such as receipts is not used for training.
- Billing account: "My Maps Billing Account" (prepay), shared with Dang's other projects.
- Budget `yolo-ecosystem-gemini`: spend cap enforcement on Gemini API in `yolo-ecosystem`, monthly, at the credit amount, so Gemini pauses instead of charging more. A spend cap covers one project and one service, so a new paid API needs its own budget.
- Per-day quotas for Gemini could not be found in the console (2026-11-12), so each app also enforces its own daily limit.

## Candidate uses

Not set up yet; listed so other apps reuse the same project and credit:

- Cloud Storage bucket for MongoDB backups of the shared `ecosystem-mongo` (DAN-64, before the first real users). Cloudflare R2 is the alternative.
- Cloud Scheduler / Cloud Run jobs for scheduled work that should not run on the VPS.

## Rules

- One API key per app and purpose, restricted to the APIs it needs, stored in that app's Doppler project (not `shared`).
- Turning on a new API here affects only billing; note it in the table above.
