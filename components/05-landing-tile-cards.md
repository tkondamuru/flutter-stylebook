# Component: Landing tile cards

## What it is
The landing page's option tiles: two-column cards, each with a black circled
icon on the left and an uppercase title on the right. Together with the
bottom navigation, they expose every option the app offers — no hamburger
menu.

## Where it lives
- `lib/presentation/widgets/home_action_grid.dart` (`HomeActionGrid`,
  `_buildActionCard`)

## The rules (the TJ way)
1. Layout: rows of TWO cards (`Row` + 2 `Expanded`, 12px gaps between cards
   and rows). On smaller devices one per row is acceptable — the rule is a
   minimum of visual density, not a fixed column count.
2. Card: white, radius 8, fixed height 82, padding 12, shadow black 0.3 /
   blur 4 / offset (0, 2). The shadow is what lifts the card off the white
   app background.
3. Left: black circle 44x44 with the icon in brand cyan `0xFF4AC9E3` at 24px.
4. Right: title in UPPERCASE, 14px, w500, black. No custom font anywhere in
   the app (theme sets no `fontFamily`) — the look IS the platform default
   (Roboto/SF) at this size/weight/casing. To keep the style, specify
   size + weight + uppercase, never a typeface.
5. Title text: ONE line preferred, TWO lines max, never more
   (`maxLines: 2`, ellipsis). The code doesn't enforce this yet — the rule
   does.
6. Whole card is tappable: `InkWell` with matching `borderRadius: 8` so the
   ripple stays inside the card.

## Code sample
```dart
Row(
  children: [
    Expanded(child: _tile(Icons.favorite_outline, 'Favorites', onTap)),
    const SizedBox(width: 12),
    Expanded(child: _tile(Icons.check_circle_outline, 'Top sundries', onTap)),
  ],
),

Widget _tile(IconData icon, String title, VoidCallback? onTap) {
  return InkWell(
    onTap: onTap,
    borderRadius: BorderRadius.circular(8),
    child: Container(
      height: 82,
      padding: const EdgeInsets.all(12),
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(8),
        boxShadow: [
          BoxShadow(
            color: Colors.black.withValues(alpha: 0.3),
            blurRadius: 4,
            offset: const Offset(0, 2),
          ),
        ],
      ),
      child: Row(
        children: [
          Container(
            width: 44,
            height: 44,
            decoration: const BoxDecoration(
              color: Colors.black,
              shape: BoxShape.circle,
            ),
            child: Icon(icon,
                color: const Color(0xFF4AC9E3), size: 24),
          ),
          const SizedBox(width: 12),
          Expanded(
            child: Text(
              title.toUpperCase(),
              maxLines: 2, // one preferred, two max, never more
              overflow: TextOverflow.ellipsis,
              style: const TextStyle(
                fontSize: 14,
                fontWeight: FontWeight.w500,
              ),
            ),
          ),
        ],
      ),
    ),
  );
}
```

## Do
- Keep titles short enough to fit two lines; rewrite the label before
  shrinking the text.

## Don't
- Never let a tile title run past two lines.
- Never introduce a custom font to "match" this look — the look is
  size/weight/casing on the system font.

## Notes for training pairs
- "Build landing-page tile cards: 2 per row, white rounded shadow cards, black circle cyan icon left, uppercase title right, max 2 lines"
- "Fix this tile: the title runs to 3 lines" (input: no maxLines)
