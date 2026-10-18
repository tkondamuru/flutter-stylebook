# Component: Bottom navigation

## What it is
The app's bottom navigation bar: black, full-width, four items with white
icons + titles, active tab in brand cyan, red badge support (cart). Rendered
on every post-login page through the shared layout.

## Where it lives
- `lib/widgets/custom_footer.dart` (`CustomFooter`)
- Wired in: `lib/widgets/app_layout.dart` (`bottomNavigationBar: CustomFooter(...)`)

## The rules (the TJ way)
1. The bar spans the FULL width, edge to edge — no margins, no horizontal
   padding. (The rounded corners in screenshots are the phone's own screen
   corners, not the widget's.)
2. Background is solid black (`Colors.black`) with a soft top shadow
   (`black26`, blur 8, offset (0, -2)) separating it from the white content.
3. Fixed height 65, wrapped in `SafeArea(top: false)` so it extends into the
   bottom system inset.
4. Each item: icon (28px) with the title directly below it (4px gap, `Column`
   with `mainAxisSize.min`). Tap area uses `HitTestBehavior.opaque`.
5. Inactive: white icon + white title (12px, w400). Active: brand cyan
   `0xFF4AC9E3` icon + cyan title (14px, w600). Color AND size/weight change
   together — the active tab is unmissable.
6. Badge support on any item (cart in the app): red circle, white 10px bold
   text, min 18x18, positioned top-right of the icon (`right: -6, top: -4`),
   thin black border (1.5) so it reads on the black bar, count capped at
   `99+`, shown only when count > 0.
7. The widget is dumb: `currentIndex` + `onItemTapped(index)` callback +
   `cartBadgeCount`. Navigation decisions live outside it.

## Code sample
```dart
class AppBottomNav extends StatelessWidget {
  final int currentIndex;
  final ValueChanged<int>? onItemTapped;
  final int? cartBadgeCount;

  const AppBottomNav({
    super.key,
    this.currentIndex = 0,
    this.onItemTapped,
    this.cartBadgeCount,
  });

  static const _cyan = Color(0xFF4AC9E3);

  @override
  Widget build(BuildContext context) {
    return Container(
      // Edge to edge: no margins, no horizontal padding.
      decoration: const BoxDecoration(
        color: Colors.black,
        boxShadow: [
          BoxShadow(
            color: Colors.black26,
            blurRadius: 8,
            offset: Offset(0, -2),
          ),
        ],
      ),
      child: SafeArea(
        top: false,
        child: SizedBox(
          height: 65,
          child: Row(
            mainAxisAlignment: MainAxisAlignment.spaceAround,
            children: [
              _item(Icons.home_outlined, 'Home', 0, null),
              _item(Icons.search, 'Search', 1, null),
              _item(Icons.shopping_bag_outlined, 'Cart', 2,
                  cartBadgeCount),
              _item(Icons.history, 'History', 3, null),
            ],
          ),
        ),
      ),
    );
  }

  Widget _item(
      IconData icon, String label, int index, int? badgeCount) {
    final isSelected = currentIndex == index;
    return GestureDetector(
      onTap: () => onItemTapped?.call(index),
      behavior: HitTestBehavior.opaque,
      child: Container(
        padding: const EdgeInsets.symmetric(vertical: 8),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Stack(
              clipBehavior: Clip.none,
              children: [
                Icon(
                  icon,
                  color: isSelected ? _cyan : Colors.white,
                  size: 28,
                ),
                if (badgeCount != null && badgeCount > 0)
                  Positioned(
                    right: -6,
                    top: -4,
                    child: Container(
                      padding: const EdgeInsets.all(4),
                      decoration: BoxDecoration(
                        color: Colors.red,
                        shape: BoxShape.circle,
                        border: Border.all(
                            color: Colors.black, width: 1.5),
                      ),
                      constraints: const BoxConstraints(
                        minWidth: 18,
                        minHeight: 18,
                      ),
                      child: Center(
                        child: Text(
                          badgeCount > 99
                              ? '99+'
                              : badgeCount.toString(),
                          style: const TextStyle(
                            color: Colors.white,
                            fontSize: 10,
                            fontWeight: FontWeight.bold,
                            height: 1,
                          ),
                        ),
                      ),
                    ),
                  ),
              ],
            ),
            const SizedBox(height: 4),
            Text(
              label,
              style: TextStyle(
                color: isSelected ? _cyan : Colors.white,
                fontSize: isSelected ? 14 : 12,
                fontWeight:
                    isSelected ? FontWeight.w600 : FontWeight.w400,
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

## Do
- Keep the widget navigation-agnostic: index in, tap callback out.

## Don't
- Never add margins or horizontal padding — it must bleed to both edges.
- Never use the framework's default `BottomNavigationBar` styling; this
  custom bar IS the style.

## Notes for training pairs
- "Build a bottom navigation bar: black full-width bar, white icons with titles below, active tab in brand cyan, red badge on the cart"
- "Convert this default BottomNavigationBar to my black bottom-nav style" (input: framework widget)
- "Add red badge support to this bottom nav item" (input: item without badge)
