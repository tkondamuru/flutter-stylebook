# General rule: No nested vertical scrolls

## What it is
Cross-cutting rule: a page has exactly ONE vertical scroll, at the page
level. Never a scrollable inside a scrollable.

## Where it lives
- `lib/presentation/pages/ship_to_selection_page.dart`: a single
  `SingleChildScrollView` wraps a plain `Column` of cards. The cards
  themselves are not scrollable.

## The rules (the TJ way)
1. ABSOLUTELY no nested vertical scroll views. One `SingleChildScrollView`
   at the page level; everything inside is a non-scrolling `Column`.
2. Screens are designed/optimized for this — content is sized and spaced so
   the single page scroll suffices.
3. Overlays are exempt: a bottom sheet or dialog may scroll internally
   (e.g. `DraggableScrollableSheet`), because it is not nested inside the
   page scroll — it replaces it while open.

## Code sample
```dart
// One scroll at the page level. Cards are plain containers in a Column.
SingleChildScrollView(
  padding: const EdgeInsets.all(16),
  child: Column(
    crossAxisAlignment: CrossAxisAlignment.start,
    children: [
      _buildHeader(),
      const SizedBox(height: 16),
      _buildSearchBar(),
      const SizedBox(height: 16),
      ...items.map((item) => _buildCard(item)),
    ],
  ),
)
```

## Do
- When a page grows, extend the single `Column` — never wrap a section in
  its own scroll view.

## Don't
- Never put a `ListView` (or another `SingleChildScrollView`) inside the
  page's `SingleChildScrollView`. If you catch yourself adding
  `shrinkWrap: true` + `NeverScrollableScrollPhysics` to silence the
  conflict, flatten the layout instead.

## Notes for training pairs
- "Convert this nested-scroll layout to my convention: one scroll at the page level" (input: ListView-in-ScrollView)
