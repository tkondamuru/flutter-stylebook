# Component: Page header

## What it is
The standard top bar for inner pages: circular black back button on the
left, centered title, and a reserved slot on the right for a page-specific
action icon.

## Where it lives
- `lib/widgets/custom_header.dart` (`CustomHeader`)

## The rules (the TJ way)
1. LEFT: always a circular black back button — 44x44 black circle,
   white `arrow_back` icon at 24px. Big, easy to tap, goes to the previous
   page. (On the home view the same circle holds the hamburger menu instead.)
2. RIGHT: always reserve a 44px-wide slot for a page-specific action icon —
   even on pages that don't have one (`actionButton ?? SizedBox(width: 44)`).
   The reservation is the rule: it keeps the title centered and gives every
   page a place for its own icon (here: the QR scan icon).
3. CENTER: the title sits in an `Expanded` + `Center`, 18px w600 black, with
   ellipsis overflow. Because left (44) and right (44) balance, the title is
   truly centered.
4. Header is 60 high with 16px horizontal padding; background matches the
   page (white here) and the system status bar stays visible above it
   (`SafeArea(bottom: false)`, no status-bar hiding — see the status-bar rule).

## Code sample
```dart
Container(
  color: Colors.white,
  child: SafeArea(
    bottom: false,
    child: Container(
      height: 60,
      padding:
          const EdgeInsets.symmetric(horizontal: 16, vertical: 8),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween,
        children: [
          // LEFT: circular black back button, always 44x44.
          GestureDetector(
            onTap: onBackPressed,
            child: Container(
              width: 44,
              height: 44,
              decoration: const BoxDecoration(
                color: Colors.black,
                shape: BoxShape.circle,
              ),
              child: const Icon(
                Icons.arrow_back,
                color: Colors.white,
                size: 24,
              ),
            ),
          ),
          // CENTER: title, truly centered between the two 44px slots.
          Expanded(
            child: Center(
              child: Text(
                title,
                style: const TextStyle(
                  fontSize: 18,
                  fontWeight: FontWeight.w600,
                  color: Colors.black,
                ),
                overflow: TextOverflow.ellipsis,
              ),
            ),
          ),
          // RIGHT: page-specific action, or the reserved empty slot.
          actionButton ?? const SizedBox(width: 44),
        ],
      ),
    ),
  ),
)
```

## Do
- Keep the 44/44 balance — left button and right slot are always the same
  width so the title never drifts.

## Don't
- Never drop the right slot on pages without an action; an unreserved row
  lets the title slide off-center.
- Never shrink the back button below 44px — it's a thumb target.

## Notes for training pairs
- "Build a page header: circular black back button left, centered title, reserved right slot for a page-specific action"
- "Fix this header: the title goes off-center when there's no right icon" (input: row without reserved slot)
