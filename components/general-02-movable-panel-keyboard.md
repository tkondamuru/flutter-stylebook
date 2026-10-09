# General rule: Movable panel (keyboard-friendly Y-drag)

## What it is
Cross-cutting rule for any screen with text fields + a submit button: when the
keyboard opens on a small screen and would cover the button, the content panel
is freely draggable in the Y axis — the user pushes it up, types, taps the
button. When the keyboard closes (or the user taps out), the panel pins back
to its original centered position. No custom drag code; pure Flutter
primitives.

## Where it lives
- `lib/presentation/pages/auth_login_page.dart` (lines 102–145): Scaffold +
  `SingleChildScrollView` + manual `viewInsets` padding.

## The rules (the TJ way)
1. `Scaffold(resizeToAvoidBottomInset: false)` — the framework must NOT
   resize the body when the keyboard opens. The background stays full-screen
   and nothing jumps.
2. Wrap the content in a `SingleChildScrollView` — this is what makes the
   panel draggable in Y. No `ScrollController`, no custom gesture code.
3. Bottom padding of the scroll view = `MediaQuery.of(context).viewInsets.bottom`
   + base padding (24). The scrollable area grows by exactly the keyboard
   height, so there is always room to drag the button above the keyboard.
4. Inside the scroll view: `ConstrainedBox(minHeight: constraints.maxHeight
   - verticalPadding)` (needs a `LayoutBuilder` for the constraints) with a
   `Center` child. Keyboard closed → content fills the viewport and sits
   centered (the "original position"). Keyboard open → content exceeds the
   shrunken viewport → draggable. Keyboard closed again → insets shrink back
   and the panel pins itself back to center automatically.
5. The keyboard does NOT auto-scroll to the focused field — the user drags
   manually. That is the intended behavior, not a bug.
6. Trigger phrase: when TJ asks for a "flexible panel" or "movable panel",
   apply exactly this recipe.

## Code sample
```dart
Scaffold(
  // 1. Never let the framework resize the body for the keyboard.
  resizeToAvoidBottomInset: false,
  body: SizedBox.expand(
    child: Stack(
      fit: StackFit.expand,
      children: [
        // Optional: full-screen background stays put.
        Image.asset('assets/images/SignInHome.png', fit: BoxFit.cover),
        SafeArea(
          child: LayoutBuilder(
            builder: (context, constraints) {
              // 2. The scroll view is what makes the panel Y-draggable.
              return SingleChildScrollView(
                // 3. Scroll area grows with the keyboard height.
                padding: EdgeInsets.only(
                  left: 24,
                  right: 24,
                  top: 24,
                  bottom:
                      MediaQuery.of(context).viewInsets.bottom + 24,
                ),
                // 4. Fills viewport when keyboard is closed (centered);
                //    exceeds it when open (draggable); re-centers on close.
                child: ConstrainedBox(
                  constraints: BoxConstraints(
                    minHeight: constraints.maxHeight - 48,
                  ),
                  child: Center(
                    child: ConstrainedBox(
                      constraints:
                          const BoxConstraints(maxWidth: 400),
                      child: _buildPanel(context),
                    ),
                  ),
                ),
              );
            },
          ),
        ),
      ],
    ),
  ),
);
```

## Do
- Use this on every screen where a keyboard could cover its submit button.
- Keep `resizeToAvoidBottomInset: false` paired with the manual
  `viewInsets.bottom` padding — one without the other breaks the pattern.

## Don't
- Never rely on the default `resizeToAvoidBottomInset: true` behavior
  (framework squish) — it jumps the background and fights the drag.
- Never add custom drag-gesture code; the scroll view already does it.
- Never use a fixed `Column` that the keyboard can cover with no way to
  reveal the button.

## Notes for training pairs
- "Build a screen with a movable panel: I can drag it up in Y when the keyboard opens; it pins back to center when the keyboard closes"
- "Convert this fixed-column screen (keyboard covers the button) to my movable panel pattern" (input: naive version)
