# Component: Shop selection page (preselection / setting page)

## What it is
A **preselection page** (TJ's term; also "setting page"): a screen that must
be completed before the user lands on home. One navigation path: setting
already done → go home; not done → set the shop and continue, or log out.

## Where it lives
- `lib/presentation/pages/ship_to_selection_page.dart`
  (`ShipToSelectionPage`, ~1040 lines)
- Layout: `lib/widgets/simple_auth_layout.dart` — black header, bigger logo,
  no footer, exactly one header icon.

## The rules (verified in code)
1. Layout is `SimpleAuthLayout`: black header with the bigger logo
   (`Image.asset('assets/images/pgw-logo.png', height: 40)`), NO footer, and
   exactly one header icon — home (when a setting is already done) or logout
   (when not). Larger logo + larger fonts: built for readability.
2. Heading is minimal with the count baked in (`'Address (3)'`, 18px bold),
   and the next setting control (`Change delivery` switch) sits on the SAME
   line — saves vertical space.
3. Page padding is `EdgeInsets.all(16)` — small, doesn't eat screen space,
   but enough that nothing looks crowded.
4. The current selection is prominent: sorted first in the list, green border
   (`Colors.green.shade600`, width 2.0), green `Current` button with a check
   icon, and a green-tinted route/date info panel.
5. Re-tapping the current selection navigates immediately with ZERO API/DB
   calls — `_handleSelectShipTo` early-returns (`context.go('/search')`)
   when the tapped shop equals the current one and delivery isn't being
   changed. The API fires only on an actual change. (Note: the code routes to
   `/search`, not `/home` — recorded as-is.)
6. Non-selected cards: cyan border (`0xFF4AC9E3`, width 1.5), cyan `Select`
   button with a truck icon, elevation 2.
7. Card text: shop name 16px bold blue (`0xFF0b9ee7`); address 15px w500
   `grey.shade700`.
8. Credit-hold shops: disabled `Locked` button + red warning banner
   (`red.shade50` bg, `red.shade200` border, 11px red text).
9. The Select/Current buttons follow the action-button rule: in-button
   spinner (`_loadingShipToId`) while the select API runs, button disabled.

## Stated rules (TJ's — not yet in code)
- Compact card option: tighter line spacing, button on the third line, or
  icon-tap to select instead of a button.
- Wizard: multiple setting pages render as hidden panels within the SAME
  screen; the Android back button always lands on the first setting page;
  every page ends with a clear next step, or a clear save once the minimum
  required settings are done.
- Nice-to-have: tapping outside the search field dismisses the keyboard
  (currently requires the done/tick key).

## Do
- Keep one navigation path per setting state — no ambiguous exits.
- Sort the current selection first; make it unmissable.

## Don't
- Never fire an API call when the selection didn't actually change.
- Never add a footer to a preselection page.

## Notes for training pairs
- "Build a preselection setting page: option cards, current selection prominent, re-tap navigates with no API call, API only on change"
- "Build the wizard heading row: minimal heading with count left, next-setting control on the same line"
