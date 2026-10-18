# General rule: Icon treatment

## What it is
Cross-cutting rule: icons are always vector, and the signature presentation
is a black circle with a brand-cyan vector icon inside.

## Where it lives
- `lib/presentation/widgets/home_action_grid.dart` — black 44 circle +
  cyan 24 icon (the tile pattern).
- `lib/widgets/custom_header.dart` — black 44 circle + WHITE 24 icon
  (the back button variant).
- Verified: `Image.asset` appears exactly 3 times in the whole app, all
  brand imagery (login background, header/auth logos) — zero icons are
  images.

## The rules (the TJ way)
1. Icons are ALWAYS vector (`IconData`, Material icons). Never PNG/JPG
   assets for icons — vectors stay sharp at every size and need no asset
   pipeline.
2. Wherever an icon is chosen as a visual anchor, present it as: black
   circle 44x44 (`BoxShape.circle`) + brand cyan `0xFF4AC9E3` icon at 24px.
3. Variant: navigation chrome (the header back button) uses the same black
   circle with a WHITE icon — white for chrome, cyan for feature icons.
4. Bottom-nav items are NOT circled — plain 28px icons. Circles mark
   anchored/standalone icons, not navigation items.

## Code sample
```dart
// Signature treatment: black circle + cyan vector icon.
Container(
  width: 44,
  height: 44,
  decoration: const BoxDecoration(
    color: Colors.black,
    shape: BoxShape.circle,
  ),
  child: const Icon(
    Icons.qr_code_scanner, // always IconData, never an image asset
    color: Color(0xFF4AC9E3),
    size: 24,
  ),
)
```

## Do
- Reach for the black-circle treatment whenever an icon needs visual weight.

## Don't
- Never ship an icon as an image asset.
- Never recolor the circle treatment arbitrarily — cyan for features, white
  for navigation chrome.

## Notes for training pairs
- "Build an icon button: vector icon in a black circle, brand cyan icon"
- "This uses a PNG as an icon — convert to my vector icon convention" (input: Image.asset icon)
