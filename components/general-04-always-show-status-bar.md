# General rule: Always show the system status bar

## What it is
Cross-cutting rule: every screen shows the system status bar (time, signal,
wifi, battery). It is never hidden, never transparent-over-content in a way
that obscures it — it is styled to match the screen, not removed.

## Where it lives
- `lib/widgets/app_layout.dart` (lines 114–125): `AnnotatedRegion` matching
  the status bar color to the header background, with luminance-adaptive icon
  brightness.
- `lib/widgets/simple_auth_layout.dart` (lines 22–28): black status bar with
  light icons for the dark auth header.
- Notably: zero `SystemChrome.setEnabledSystemUIMode` calls anywhere in the
  app — immersive/fullscreen mode is never entered.

## The rules (the TJ way)
1. The status bar is ALWAYS visible on every screen. No exceptions, no
   "immersive" moments.
2. Never call `SystemChrome.setEnabledSystemUIMode` to hide system UI. If you
   see it in generated code, delete it.
3. Style the bar, don't hide it: wrap each screen (or the shared layout) in
   `AnnotatedRegion<SystemUiOverlayStyle>`.
4. The status bar background matches the screen's header/top color.
5. Icon brightness adapts to that color via a luminance check: dark
   background → light icons, light background → dark icons. Set all three:
   `statusBarColor` + `statusBarIconBrightness` (Android), and
   `statusBarBrightness` (iOS — note its inverted semantics: `Brightness.dark`
   there means light icon *text*).
6. Use `SafeArea` so content starts below the status bar instead of colliding
   with it — even on full-bleed background screens like login.

## Code sample
```dart
AnnotatedRegion<SystemUiOverlayStyle>(
  value: SystemUiOverlayStyle(
    // Match the screen's header/top color.
    statusBarColor: headerBackgroundColor ?? Colors.white,
    // Android: pick icon brightness by luminance.
    statusBarIconBrightness:
        headerBackgroundColor != null &&
                headerBackgroundColor.computeLuminance() < 0.5
            ? Brightness.light // white icons on dark bar
            : Brightness.dark, // black icons on light bar
    // iOS: inverted semantics — Brightness.dark = light text.
    statusBarBrightness:
        headerBackgroundColor != null &&
                headerBackgroundColor.computeLuminance() < 0.5
            ? Brightness.dark
            : Brightness.light,
  ),
  child: Scaffold(
    body: SafeArea(
      child: /* screen content */,
    ),
  ),
);
```

## Do
- Put the `AnnotatedRegion` in the shared app layout so every screen inherits
  it; override per screen only when the header color differs.

## Don't
- Never use fullscreen / immersive / sticky-immersive system UI modes.
- Never draw content over the status bar without `SafeArea` to protect it.

## Notes for training pairs
- "Build an app shell where the status bar is always visible, matches the header color, and adapts icon brightness"
- "Fix this screen: it hides the status bar in immersive mode" (input: SystemChrome immersive code)
