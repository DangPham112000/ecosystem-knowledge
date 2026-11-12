# UI guidelines

Rules every app's UI follows. Shared look (colors, tokens, font): see the 2026-11-10 theme entry in [decisions.md](decisions.md).

## Disabled controls say why

_Decided on 2026-11-12 (Dang, from Bill Splitter's "Make treasurer" button)._

A button, input or other control that is disabled because of something the user can understand or fix always explains why, in a tooltip:

- **Desktop:** shown on mouse hover.
- **Phone / touch:** shown when the user taps the disabled control. It closes on a second tap, a tap elsewhere, scrolling, or after about 4 seconds.
- **Keyboard:** shown on keyboard focus, closed with Escape.

How to write the reason:

- One or two short sentences: what is missing and what to do, e.g. "Lan hasn't linked an account yet. They can become treasurer after joining with the invite link while signed in."
- Use the person's or item's name when there is one.

Exceptions: no tooltip is needed while a control is only busy and its label already says so ("Saving…", "Loading…"), or when visible text right next to it already gives the same reason. A plain `title` attribute is not enough: it never shows on touch screens.

Implementation notes:

- A disabled `<button>` receives no pointer events, so wrap it in an element that takes hover and tap, and give the child `pointer-events: none`. Make the wrapper focusable and link the tooltip with `aria-describedby`.
- Keep the tooltip inside the screen (clamp to the viewport edges, flip below when there is no room above).
- Reference implementation: `DisabledReason` in Bill Splitter (`frontend/src/components/DisabledReason.tsx`, bill-splitter #19). Copy it rather than inventing a new pattern. Apps on shadcn/Base UI (Journei) can build the same behaviour on their Tooltip/Popover primitives as long as tap works.
