# Bill Splitter

- Repo: [DangPham112000/bill-splitter](https://github.com/DangPham112000/bill-splitter). Not deployed, no deploy workflow yet.
- Stack: pnpm monorepo; frontend React 19 + Vite + Tailwind + Apollo Client; backend Express + Apollo GraphQL + Mongoose; `shared/` holds money helpers used by both (VND by default, also THB, CNY, ...).
- Splits costs for a trip, project or party. Members join through a link without signing in; the app works out who pays whom with the fewest transfers.
- Sign-in: none yet. `Splitter.adminUserId` and `Member.userId` are strings waiting for a clerkId. Plan: optional Clerk sign-in, anonymous members still allowed, `userId` attached when they sign in (DAN-84).
- Secrets: Doppler project `bill-splitter`, no production secrets yet.
- Target of the data-sharing flows Journei → Bill Splitter and Event Manager → Bill Splitter.
