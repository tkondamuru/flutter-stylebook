# General rule: No hamburger menus

## What it is
Cross-cutting design stance (TJ's): the app never hides navigation behind a
hamburger menu. All options are visible up front — the landing page's tile
cards plus the bottom navigation expose everything the app can do.

## Where it lives
- Stated rule — and note the code still *supports* a hamburger
  (`CustomHeader` has `isHomeView`/`onMenuPressed`), so this rule is about
  what TJ builds, not what the widgets allow.

## The rules (the TJ way)
1. REFRAIN from hamburger menus. No drawer hiding the app's options.
2. The landing page carries the full option set as tile cards (see the
   landing-tile-cards component) — the most frequently used options,
   visible immediately.
3. The bottom navigation carries the persistent sections (Home, Search,
   Cart, History).
4. Tiles + bottom nav together must cover every available option. A tile
   may open a sub-screen with its own sub-options — depth is fine, hiding
   is not.

## Do
- When adding a feature, give it a tile on the landing page and/or a bottom
  nav slot — never only a drawer entry.

## Don't
- Never add a `Drawer` / hamburger menu to a new app built in this style.

## Notes for training pairs
- "This app uses a hamburger drawer — convert its navigation to my convention" (input: Scaffold with drawer)
