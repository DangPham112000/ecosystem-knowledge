# Design principles

How every ecosystem app (Event Manager, Journei, News, Bill Splitter, the homepage and any new app) is designed. Decided by Dang on 2026-11-15; see [decisions.md](decisions.md). Concrete UI rules (disabled controls, ...) live in [ui-guidelines.md](ui-guidelines.md); this file is the "why" and the overall shape they follow.

## The idea in one paragraph

**Users mostly talk to an AI agent, and the agent talks to our apps.** The agent is either one the user already has (Claude, ChatGPT, ...) connected to our MCP servers, or a bot we build ourselves. It creates and edits data, checks for problems before writing (for example calendar conflicts) and answers questions. The web UI is where the user **looks at** their information and makes **small, quick edits**. Typing into forms is the fallback, not the main path.

## 1. The UI shows information first

- Every screen answers "what do I need to know right now?" before "what can I do?". Lists, timelines, calendars, balances and maps take the space; controls stay out of the way.
- Show the state that matters without a click: totals, who owes whom, the next event, conflicts, what is missing. Warnings (an overlap, an unbalanced bill) appear next to the item, not in a dialog.
- Empty states say what the screen will show and, in one line, how to fill it **from an agent** (for example "Ask your assistant: plan 3 days in Da Nang"), with a small manual button as the alternative.

## 2. Edit in place, not on separate edit pages

- Click a value (title, time, amount, name) to edit it right there; save on Enter or blur, cancel on Escape. No "Edit" page that re-shows the whole object as a form.
- Prefer undo over "Are you sure?" dialogs. Keep a confirmation only for actions that cannot be undone (deleting a splitter with expenses, a plan with activities) and state what will be lost.
- Changes save immediately; show a quiet "Saved" or an inline error, never a full-page reload.

## 3. Few buttons, minimal forms

- **One primary action per screen** (a filled capsule button). Everything else is secondary (ghost or icon button) or lives in an overflow menu (`⋯`).
- **Forms show only required fields by default.** Optional fields sit behind a single toggle ("More options", a switch or a disclosure row). Rules:
  - The toggle starts **off** when creating, and starts **on** when editing an object that already has optional values, so nothing the user entered is hidden.
  - Every optional field has a sensible default (current time zone, VND, "now + 1 hour", the user as payer), so submitting with the toggle off always works.
  - **Defaults are never invisible.** While the toggle is off, the defaults that will be saved are shown as a short line of read-only text, so nothing appears after saving that the user did not see. Example (Bill Splitter, add expense): under Name and Amount, the line "Paid by you · split equally between 4 · VND · today". Tapping the line (or any value in it) turns the toggle on and opens that field for editing. Defaults that save nothing (an empty description or location) are not listed.
  - Remember the toggle per form in `localStorage` only as a convenience; never store data there.
  - An error in a hidden field (a split that does not add up, a missing name) turns the toggle on and focuses that field.
  - Reference implementation: Bill Splitter's `MoreOptionsToggle` (`frontend/src/components/MoreOptions.tsx`) and `useMoreOptions` (`frontend/src/lib/moreOptions.ts`), used on the expense and splitter forms (DAN-131, bill-splitter #25). Copy them rather than inventing a new pattern.
- Fewer fields beats clever layout: if a field can be inferred (an icon from the title, a currency from the destination, an end time from the start time), infer it and let the user change it afterwards in place.
- A form never asks for something an agent or another app already knows (attendees already in the Event Manager event, members already in the splitter).

## 4. Agent-first: everything a form does, a tool does

- **Parity rule:** any create or edit the UI offers is also an MCP tool, with the same validation and the same defaults. A new form field is not done until the matching tool accepts it. Deletes are the one deliberate exception (see section 6).
- **Agents also inform.** Read and check tools let the agent answer "am I free Saturday?", "who still owes me?", "what's new about running?" without the user opening the app. The best example today is Event Manager: `create_event` checks overlaps itself and refuses with the conflicting events, and `check_availability` / `find_conflicts` let the agent look before it writes.
- **The UI and the agent share one backend path.** MCP tools call the same resolvers or data layer as GraphQL (Journei's tools call the resolvers; Event Manager's call the shared data source), so ownership checks and rules exist once.
- What the agent did should be visible in the UI the next time the user looks, with nothing to refresh by hand.

## 5. Visual baseline: liquid glass

The shared look is Bill Splitter's "liquid glass" style on Event Manager's palette (orange primary, violet-tinted neutrals, Geist): translucent blurred panes, capsule buttons, `rounded-3xl` cards, solid fallbacks for `prefers-reduced-transparency`. Copy the `glass*` utilities and token values from Bill Splitter's `frontend/src/index.css` rather than inventing new ones. The homepage and Event Manager already use it; Journei and News adopt it when redesigned. Details: decisions.md entries of 2026-11-10 and 2026-11-12.

## 6. MCP tool design

Rules for every app's MCP server, so an agent can do everything the forms do, safely.

### Server

- Endpoint `POST /mcp`, Streamable HTTP, stateless (a fresh server per request) as in Event Manager and Journei.
- **Two ways in:** Clerk OAuth (for claude.ai and other connector UIs) and a personal API key (for headless bots and scripts, e.g. Event Manager `emk_`, News `nwk_`, Bill Splitter `bsk_`). An app that has only one of them should add the other. Only the app itself may create or revoke keys: refuse session tokens issued to another ecosystem app (Event Manager `requireOwnApp`, Bill Splitter the same).
- **Apps with device-bound rights** (Bill Splitter) give each account one server-side "AI agent" device, so agent writes follow the same device rules as a phone's.
- **Server `instructions` give the agent its context**, built per request: whose data this is, the current time and the user's time zone, the format for times (ISO 8601 with offset), and the app's rules in one line each (for example "only events the user created can be updated"). Event Manager's `createMcpServer` is the model.
- For recurring jobs, publish an MCP **prompt** that spells out the workflow (News `update_topic_news`), so the user does not have to write it.

### Tools

- **Names:** `snake_case`, verb first, same verbs everywhere: `list_*`, `get_*`, `create_*`, `update_*`, `delete_*`, plus `check_*` / `find_*` for read-only checks and `upsert_*` for idempotent bulk writes.
- **Schema mirrors the form:** required form fields are required in the input schema; optional fields are optional, with the default described in `.describe()` exactly as the UI applies it. Use enums for fixed choices and limits (`max(200)`) that match the backend.
- **Annotations:** `readOnlyHint: true` on every read and check tool, `destructiveHint: true` on deletes, `idempotentHint: true` on upserts. Connector UIs use them to decide when to ask the user.
- **Guard before writing.** A write that could surprise the user checks first and refuses with the reason and the data needed to decide, instead of writing silently. Pattern (Event Manager `create_event`): return `{ created: false, reason, conflicts }`, and accept an explicit override flag (`allowConflict`) that the agent sends only after the user agreed. Use the same pattern for duplicates (a near-identical expense, a place already in the plan).
- **Offer the check as its own read-only tool** too (`check_availability`), so the agent can ask before proposing.
- **Results are structured and readable:** return JSON with ids, plus human-friendly values the agent can quote directly (local time strings, formatted money). Return what changed (`created: true, event`) so the agent can confirm without another call.
- **Errors are tool results, not exceptions:** `isError` with one sentence on what to fix ("endTime must be after startTime"). Never leak stack traces or other users' data; "not found" for objects the user cannot see.
- **Get-or-create and upsert** where repeats are likely (Journei categories and destinations, News articles by URL), so an agent retry never creates duplicates.
- **Deletes:** expose them only where the UI makes them easy and they can be undone or are low-stakes; otherwise leave deletion to the UI. Today Event Manager exposes `delete_event` (owner only) and Journei deliberately exposes none.
- **Same permissions as the UI:** ownership is checked in the shared resolver or data layer, never only in the MCP layer.

### Cross-app

- Each app exposes its own MCP server; the **agent** combines them (for example, check Event Manager availability before adding a Journei activity). An app does not proxy another app's tools.
- MCP calls carry Clerk OAuth tokens, which other apps' GraphQL does not accept, so server-to-server sync triggered by an MCP write happens later (Journei's "Sync now"). A tool result should say so when it matters.

## 7. Per-app status and next steps

| App | MCP today | Gaps against these principles |
| --- | --- | --- |
| Event Manager | `list_events`, `get_event`, `check_availability`, `find_conflicts`, `create_event`, `update_event`, `delete_event`; Clerk OAuth + `emk_` keys | Reference implementation. Add tools for any new form field (attendees, sharing) as they appear. |
| Journei | `list_plans`, `get_plan`, `list_destinations`, `list_categories`, `create_destination`, `create_category`, `recommended_places`, `create_plan`, `update_plan`, `create_place`, `update_place`, `create_scheduled_activity`, `update_scheduled_activity`; Clerk OAuth | Add server `instructions` (time zone, rules) and a read-only check that exposes `calendarConflicts`, so an agent can warn before scheduling. No API-key path for bots yet. |
| News | `list_topics`, `list_news`, `upsert_news`, prompt `update_topic_news`; `nwk_` keys only | Clerk OAuth so claude.ai connectors work; tools for any topic management the UI offers. |
| Bill Splitter | `list_splitters`, `get_splitter`, `check_duplicate_expense`, `create_splitter`, `add_member`, `add_expense`, `update_expense` (duplicate guard with `allowDuplicate`); Clerk OAuth + `bsk_` keys (DAN-125) | Tools for new forms as they land: fund contributions, transfers marked done, income, splitter settings (`update_splitter`), member rename and treasurer role. Device-bound anonymous members stay UI only; the MCP acts as the signed-in user. |
| Homepage | not applicable | Static; follows the visual baseline only. |

## Checklist for a new feature

- [ ] The screen shows the information first; at most one primary button.
- [ ] Values edit in place; no separate edit page.
- [ ] The form shows only required fields; optional ones behind the toggle, with defaults shown as read-only text.
- [ ] The same create or edit exists as an MCP tool with the same rules, and a read or check tool if the agent needs to look before writing.
- [ ] Tool annotations, server instructions and descriptions are updated.
- [ ] Glass look and shared tokens; disabled controls explain why ([ui-guidelines.md](ui-guidelines.md)).
