# General rule: Forced-exit warning

## What it is
Cross-cutting rule: the top icon (home/logout) is a FORCED exit — leaving
through it always warns that unsaved data will be lost. Stated by TJ; NOT
yet in the code (the current home/logout buttons navigate/sign out
directly).

## Where it lives
- Stated rule — no code reference yet. Applies wherever a top-bar home or
  logout icon exists (e.g. `ShipToSelectionPage`'s `headerAction`).

## The rules (the TJ way)
1. The top icon (home/logout) is a forced exit: ALWAYS show a "data will be
   lost" confirmation dialog. No exceptions.
2. NEVER track "did the data change?" to decide whether to warn — just always
   warn. Change-tracking code is hard, adds overhead, and adds no value.
3. The in-page exit path (completing the setting and continuing, the page's
   own save/done button) is the clean exit — no warning needed there.
4. Dialog follows the app's dialog conventions; on confirm, proceed with the
   exit (navigation or logout).

## Code sample
```dart
// Forced exit: always warn, never check whether data changed.
IconButton(
  icon: const Icon(Icons.home, color: Colors.white),
  tooltip: 'Home',
  onPressed: () => _confirmForcedExit(
    context,
    onConfirm: () => context.go('/home'),
  ),
)

Future<void> _confirmForcedExit(
  BuildContext context, {
  required VoidCallback onConfirm,
}) {
  return showDialog(
    context: context,
    builder: (context) => AlertDialog(
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(12),
      ),
      title: const Text('Leave without saving?'),
      content: const Text(
        'Your changes will be lost if you leave now.',
      ),
      actions: [
        TextButton(
          onPressed: () => Navigator.of(context).pop(),
          child: const Text('STAY'),
        ),
        ElevatedButton(
          style: ElevatedButton.styleFrom(
            backgroundColor: const Color(0xFF4AC9E3),
            foregroundColor: Colors.black,
          ),
          onPressed: () {
            Navigator.of(context).pop();
            onConfirm();
          },
          child: const Text('LEAVE'),
        ),
      ],
    ),
  );
}
```

## Do
- Apply to every top-bar home/logout icon on every setting/form screen.

## Don't
- Never add "only warn if dirty" logic — the rule is unconditional.
- Never warn on the in-page clean exit path.

## Notes for training pairs
- "Add my forced-exit warning to this screen's home button: always warn, no change tracking"
