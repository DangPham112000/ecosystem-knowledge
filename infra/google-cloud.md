# Google Cloud

One Google Cloud project for the whole ecosystem: **`yolo-ecosystem`** (project number 320304558326), owned by Dang. Reuse it; don't create a project per app.

| Use | Since | Details |
| --- | --- | --- |
| Google sign-in for the shared Clerk app | 2026-10-24 (DAN-82) | Custom OAuth credentials, consent screen External and published. See [clerk.md](clerk.md). |
| Gemini API for Bill Splitter receipt OCR | planned (DAN-116) | Generative Language API, key `GEMINI_API_KEY` in Doppler `bill-splitter/prd`, restricted to that API. |

## Billing

- Paid from the monthly Google Cloud credits that come with Dang's Google AI Pro plan (Google Developer Program benefit). The billing account holding those credits must be linked to `yolo-ecosystem`.
- Gemini must run on the paid tier (billing linked), so user data such as receipts is not used for training.
- Keep a budget alert at the monthly credit amount and a daily request quota on each paid API: budgets only alert, quotas stop spending.

## Rules

- One API key per app and purpose, restricted to the APIs it needs, stored in that app's Doppler project (not `shared`).
- Turning on a new API here affects only billing; note it in the table above.
