# General rule: Button loading conventions (action vs navigation)

## What it is
Cross-cutting rule: every button falls into one of two kinds, and each kind
has exactly one loading behavior. The invariant: **never fetch in the
background without a visible indicator** — the user always sees that work is
happening.

TJ's words for the two kinds are kept: **action button** and **navigation
button**. Shorthand for the difference: action buttons use *inline* loading,
navigation buttons use *full-screen* loading.

## Where it lives
- Action button: `lib/presentation/pages/auth_login_page.dart`
  (`_handleLogin` + LOGIN `ElevatedButton`) — spinner swaps into the button
  itself; the screen never changes until the API result is in.
- Navigation loading: `lib/presentation/pages/my_vin_searches_page.dart`
  (line ~131), `lib/presentation/pages/cart/cart_page.dart` (lines ~874–879)
  — destination screens render a white background with a centered spinner
  while fetching, then swap in the data.

## The rules (the TJ way)
1. **Action button** = triggers API or external work. It NEVER navigates on
   tap. Navigation may happen *as a result* of the API response, never
   before it.
2. When an action button is tapped, the spinner goes **inside the button
   itself**: the label swaps for a 20x20 `CircularProgressIndicator`
   (`strokeWidth: 2`, in the button's foreground color) and the button is
   disabled (`onPressed: null`) until the work finishes. The user stays on
   the same screen; errors surface inline (see the login error banner).
3. **Navigation button** = immediately loads another panel or screen. On tap,
   navigate at once — no waiting.
4. The destination screen owns the loading state: white background, centered
   `CircularProgressIndicator`, shown while its data is fetched; the real
   content replaces it when data arrives. With Riverpod, this is the
   `AsyncValue.when(loading: () => Center(child: CircularProgressIndicator()))`
   branch.
5. The invariant, restated: if data is being fetched in the background, the
   user sees an indicator — inline in the button for actions, full-screen on
   white for navigation. No silent background fetches, ever.

## Code sample
```dart
// --- ACTION button: spinner inside the button, no navigation on tap ---
bool _isWorking = false;

Future<void> _submitClaim() async {
  setState(() => _isWorking = true);
  try {
    final result = await ref.read(claimRepositoryProvider).submit(_form);
    if (!mounted) return;
    // Navigation (if any) follows the RESULT, never the tap.
    if (result.needsReview) context.push('/claim-review');
  } finally {
    if (mounted) setState(() => _isWorking = false);
  }
}

SizedBox(
  width: double.infinity,
  child: ElevatedButton(
    onPressed: _isWorking ? null : _submitClaim,
    style: ElevatedButton.styleFrom(
      backgroundColor: const Color(0xFF4AC9E3),
      foregroundColor: const Color(0xFF353535),
      padding: const EdgeInsets.symmetric(vertical: 16),
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(4),
      ),
      elevation: 2,
    ),
    child: _isWorking
        ? const SizedBox(
            height: 20,
            width: 20,
            child: CircularProgressIndicator(
              strokeWidth: 2,
              valueColor: AlwaysStoppedAnimation<Color>(
                Color(0xFF353535),
              ),
            ),
          )
        : const Text(
            'SUBMIT CLAIM',
            style: TextStyle(
              fontSize: 16,
              fontWeight: FontWeight.bold,
              letterSpacing: 0.5,
            ),
          ),
  ),
)
```

```dart
// --- NAVIGATION button: navigate at once; destination shows the spinner ---
ElevatedButton(
  onPressed: () => context.push('/orders'),
  // ... same TJ button style ...
  child: const Text('VIEW ORDERS', /* ... */),
)

// Destination screen: white background + spinner until data arrives.
class OrdersPage extends ConsumerWidget {
  const OrdersPage({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final orders = ref.watch(ordersProvider);
    return Scaffold(
      backgroundColor: Colors.white,
      body: orders.when(
        loading: () =>
            const Center(child: CircularProgressIndicator()),
        error: (e, _) => Center(child: Text('Failed: $e')),
        data: (list) => ListView(/* ... */),
      ),
    );
  }
}
```

## Do
- Name the kind before building the button: "does this do work, or go
  somewhere?" — then apply that kind's loading behavior, no exceptions.
- Disable action buttons while their work runs (no double-taps).

## Don't
- Never navigate on tap and *then* run the API the button was for — that is
  an action button wearing a navigation button's clothes.
- Never leave a background fetch without an indicator, even a "fast" one.

## Notes for training pairs
- "Build an action button that submits a form to an API: spinner in the button, navigate only if the result requires it"
- "Build a navigation button to a details screen that shows a white loading spinner until its data arrives"
