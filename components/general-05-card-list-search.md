# General rule: Card-list search bar

## What it is
Cross-cutting rule: any page that shows a list of cards gets a standard
search bar, and how it filters depends on where the data lives.

## Where it lives
- Client-side: `lib/presentation/pages/ship_to_selection_page.dart`
  (`TextField` with `hintText: 'Search within list'`, `onChanged` filtering,
  `_filterShipToItems`) — filters on type, no button.
- Server-side: same file, `_showSearchCustomersDialog` — an explicit
  `SEARCH` ElevatedButton fires the API.

## The rules (the TJ way)
1. The search bar is STANDARD on every card list: `TextField` with a search
   `prefixIcon`, inline `hintText` (e.g. `'Search within list'`), radius 8,
   content padding 16h/12v. It must make filtering on any visible text easy.
2. Client-side filtering: filter ON TYPE via `onChanged` → `setState`. No
   button, no submit step. Match across the card's text fields (name, id,
   city, state, zip in the shop example).
3. Server-side filtering (API): MUST have an explicit search button. No
   on-type API calls. The button follows the action-button rule (in-button
   spinner while searching).
4. Nice-to-have (not yet in code): tapping outside the search field dismisses
   the keyboard.

## Code sample
```dart
// Client-side: filter on type, no button.
TextField(
  decoration: InputDecoration(
    hintText: 'Search within list',
    prefixIcon: const Icon(Icons.search),
    contentPadding:
        const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(8),
    ),
  ),
  onChanged: (value) => setState(() => _searchQuery = value),
),

List<Item> _filtered(List<Item> items) {
  if (_searchQuery.isEmpty) return items;
  final q = _searchQuery.toLowerCase();
  return items.where((i) =>
      i.name.toLowerCase().contains(q) ||
      i.id.toString().contains(q) ||
      i.city.toLowerCase().contains(q)).toList();
}
```

```dart
// Server-side: explicit button fires the API.
ElevatedButton(
  onPressed: _isSearching ? null : _runServerSearch,
  style: ElevatedButton.styleFrom(
    backgroundColor: const Color(0xFF4AC9E3),
    foregroundColor: Colors.black,
  ),
  child: _isSearching
      ? const SizedBox(
          height: 20,
          width: 20,
          child: CircularProgressIndicator(strokeWidth: 2),
        )
      : const Text('SEARCH'),
)
```

## Do
- Put the search bar above the cards on every card list, no exceptions.

## Don't
- Never fire API calls on every keystroke.
- Never make the user tap a button for filtering that could be done locally.

## Notes for training pairs
- "Add a standard search bar to this card list: filters on type, client-side, no button"
- "This list needs server-side filtering — convert to my convention with an explicit search button" (input: onChanged version)
