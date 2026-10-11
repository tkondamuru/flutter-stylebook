# General rule: Selection card

## What it is
Cross-cutting pattern: a card representing one selectable option, where the
selected state is unmissable — border color, button color, and an info panel
all flip together.

## Where it lives
- `lib/presentation/pages/ship_to_selection_page.dart` (the per-shop
  `Container` in the list builder, ~lines 740–850).

## The rules (the TJ way)
1. Card base: white, radius 8, `margin: EdgeInsets.only(bottom: 12)`,
   `padding: EdgeInsets.all(16)`, shadow `grey` alpha 0.2, blur 8,
   offset (0, 4).
2. Border flips with selection: selected → `Colors.green.shade600`,
   width 2.0; unselected → brand cyan `0xFF4AC9E3`, width 1.5.
3. The action button flips with it: selected → green.shade600 bg, black87
   text, check-circle icon, label `Current`, elevation 0; unselected →
   cyan bg, black87 text, truck icon, label `Select`, elevation 2. Both:
   radius 6, bold label, in-button spinner while its API runs.
4. Selected cards may carry an extra info panel (green.shade50 bg,
   green.shade200 border, radius 6) with the selection's details.
5. Title line: option name 16px bold blue (`0xFF0b9ee7`); subtitle 15px w500
   `grey.shade700`.

## Code sample
```dart
Container(
  margin: const EdgeInsets.only(bottom: 12),
  decoration: BoxDecoration(
    color: Colors.white,
    borderRadius: BorderRadius.circular(8),
    border: Border.all(
      color: isSelected
          ? Colors.green.shade600
          : const Color(0xFF4AC9E3),
      width: isSelected ? 2.0 : 1.5,
    ),
    boxShadow: [
      BoxShadow(
        color: Colors.grey.withValues(alpha: 0.2),
        blurRadius: 8,
        offset: const Offset(0, 4),
      ),
    ],
  ),
  child: Padding(
    padding: const EdgeInsets.all(16),
    child: Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(
          title,
          style: const TextStyle(
            fontSize: 16,
            fontWeight: FontWeight.bold,
            color: Color(0xFF0b9ee7),
          ),
        ),
        const SizedBox(height: 10),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Expanded(
              child: Text(
                subtitle,
                style: TextStyle(
                  fontSize: 15,
                  fontWeight: FontWeight.w500,
                  color: Colors.grey.shade700,
                ),
              ),
            ),
            ElevatedButton.icon(
              onPressed: isWorking ? null : onSelect,
              icon: isWorking
                  ? const SizedBox(
                      width: 16,
                      height: 16,
                      child: CircularProgressIndicator(strokeWidth: 2),
                    )
                  : Icon(
                      isSelected
                          ? Icons.check_circle
                          : Icons.local_shipping_outlined,
                      size: 18,
                    ),
              label: Text(
                isSelected ? 'Current' : 'Select',
                style: const TextStyle(fontWeight: FontWeight.bold),
              ),
              style: ElevatedButton.styleFrom(
                backgroundColor: isSelected
                    ? Colors.green.shade600
                    : const Color(0xFF4AC9E3),
                foregroundColor: Colors.black87,
                elevation: isSelected ? 0 : 2,
                padding: const EdgeInsets.symmetric(
                  horizontal: 16,
                  vertical: 10,
                ),
                shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(6),
                ),
              ),
            ),
          ],
        ),
      ],
    ),
  ),
)
```

## Do
- Flip border, button, and info panel together — selection must read at a
  glance.

## Don't
- Never signal selection with color alone on just one element; the card and
  its button change as a unit.

## Notes for training pairs
- "Build a selection card list: white cards with shadow, green border for selected and cyan for the rest, matching Current/Select buttons"
