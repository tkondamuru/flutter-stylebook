# General rule: Text field keyboard consistency

## What it is
Cross-cutting rule, not tied to one screen: every text field in the same form
uses the same `keyboardType`, so moving between fields never swaps the
keyboard layout — which is what causes the visible flutter/resize jitter.

## Where it lives
- `lib/presentation/pages/auth_login_page.dart` (lines 217, 271): Customer ID
  and password fields are both `TextInputType.text`.

## The rules (the TJ way)
1. In any form with more than one text box, ALL fields share the same
   `keyboardType`.
2. The password field uses `TextInputType.text` + `obscureText: true` — never
   `TextInputType.visiblePassword`, which triggers a different keyboard layout.
3. No custom code needed. The entire fix is matching the keyboard types.
4. `textInputAction` still differs per field (`.next` to advance, `.done` to
   submit) — that does not change the keyboard layout, so it stays.
5. `autofillHints` (username / password) still apply normally — autofill is
   independent of keyboard type.
6. Conscious tradeoff: you lose specialized layouts like the email `@` key.
   UI stability wins over the convenience key.

## Code sample
```dart
// Every field in the form: same keyboardType.
TextFormField(
  keyboardType: TextInputType.text,
  textInputAction: TextInputAction.next,
  onFieldSubmitted: (_) =>
      FocusScope.of(context).requestFocus(_passwordFocusNode),
  ...
),
TextFormField(
  keyboardType: TextInputType.text, // NOT TextInputType.visiblePassword
  obscureText: !_isPasswordVisible,
  textInputAction: TextInputAction.done,
  ...
),
```

## Do
- Audit every new multi-field form: one keyboard type across all its fields.
- Keep `obscureText` (not the keyboard type) responsible for hiding passwords.

## Don't
- Never mix `emailAddress` / `visiblePassword` / `number` keyboards inside one
  form just for their convenience layouts.
- Never write custom keyboard-sync code — matching the types is the whole fix.

## Notes for training pairs
- "Build a 3-field form with no keyboard flutter when switching fields"
- "Fix this form: it flutters when tapping between fields" (input: mixed types)
