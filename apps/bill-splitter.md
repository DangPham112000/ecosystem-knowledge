# Bill Splitter

- Repo: [DangPham112000/bill-splitter](https://github.com/DangPham112000/bill-splitter). Live at `bills.dantepham.site` (`/opt/bill-splitter`) since 2026-10-25 (PR #4, DAN-84).
- Stack: pnpm monorepo; frontend React 19 + Vite + Tailwind + Apollo Client; backend Express + Apollo GraphQL + Mongoose; `shared/` holds money helpers used by both (VND by default, also THB, CNY, ...).
- Splits costs for a trip, project or party. Members join through a link without signing in; the app works out who pays whom with the fewest transfers.
- Sign-in: optional, with the shared Clerk app (live since 2026-10-25, DAN-84). The backend puts the Clerk user id in the GraphQL context as `clerkId` (null for anonymous members); no User table. `Splitter.adminUserId` and `Member.userId` hold Clerk user ids, assigned once the create and join APIs exist (DAN-35, DAN-41). `CLERK_AUTHORIZED_PARTIES` is only `https://bills.dantepham.site`.
- Secrets: Doppler `bill-splitter/prd` references `${shared.prd.CLERK_*}` and `${shared.prd.VPS_*}`; GitHub keeps only `DOPPLER_TOKEN`.
- Target of the data-sharing flows Journei → Bill Splitter and Event Manager → Bill Splitter.
