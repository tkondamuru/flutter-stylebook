# General rule: Post-login shell (white app, black footer)

## What it is
Cross-cutting rule, TJ's pattern for all his apps: once the user is logged
in, every page renders inside the same shell — white app background, black
bottom navigation on every page.

## Where it lives
- `lib/widgets/app_layout.dart`: `Scaffold(backgroundColor: Colors.white,
  bottomNavigationBar: CustomFooter(...))` — every page built on this layout
  gets the footer automatically.
- `lib/widgets/custom_footer.dart`: the black bar itself.

## The rules (the TJ way)
1. After login, EVERY page has the bottom navigation. No exceptions, no
   footer-less inner pages — it is part of the shell, not a per-page choice.
2. The shell owns it: pages declare their content; the shared layout adds
   the `CustomFooter` via `bottomNavigationBar`. A page never builds its own
   footer.
3. Color contract: app surfaces are white, the footer is black. White page,
   black bar — that contrast is the brand frame.
4. The active tab is resolved by the layout from the current route
   (`_resolveFooterIndex`), and taps route through a single callback
   (`onFooterItemTapped`) — navigation decisions stay outside the footer
   widget.

## Code sample
```dart
// Pages stay dumb about the shell: content in, footer automatic.
class AppShell extends StatelessWidget {
  final Widget child;
  final int currentIndex;
  final ValueChanged<int> onNavTap;
  final int? cartBadgeCount;

  const AppShell({
    super.key,
    required this.child,
    required this.currentIndex,
    required this.onNavTap,
    this.cartBadgeCount,
  });

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.white, // app surfaces are white...
      body: child,
      // ...and every page gets the black footer from the shell.
      bottomNavigationBar: AppBottomNav(
        currentIndex: currentIndex,
        onItemTapped: onNavTap,
        cartBadgeCount: cartBadgeCount,
      ),
    );
  }
}
```

## Do
- Route every post-login page through the shared shell.

## Don't
- Never build a page-local bottom nav; never ship a post-login page without
  the footer.

## Notes for training pairs
- "Build the post-login app shell: white background pages with the black bottom navigation on every page"
