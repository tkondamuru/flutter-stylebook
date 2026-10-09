# Component: Login page

## What it is
Branded sign-in screen: a full-screen company banner image with a vertically
centered, translucent form card floating over it — customer ID + password
fields and one elevated LOGIN button. This is the visual template for all
auth/entry screens.

## Where it lives
- `lib/presentation/pages/auth_login_page.dart` (`AuthLoginPage`) — the real one.
- Do NOT copy `lib/presentation/pages/login_page.dart` or
  `auth_login_test_page.dart` — older/test variants.

## The rules (the TJ way)
1. Background is a full-screen branded image, edge to edge: `Stack` +
   `Image.asset(..., fit: BoxFit.cover)` inside `SizedBox.expand`. The screen
   itself stays transparent to the image — never a flat solid-color backdrop.
2. If no full-screen brand image is available, fall back to a glass-themed
   (frosted) background — never flat color. (Stated rule; not yet in code.)
3. Content is centered vertically: `Center` + `ConstrainedBox(minHeight)`,
   max width 400, horizontal padding 24.
4. Text fields ALWAYS show the brand border — cyan `0xFF4AC9E3`, width 2,
   radius 8 — identically in `enabledBorder` and `focusedBorder`. Never
   restyle the border on focus; there is no "highlight when tapped" behavior.
5. Field fill is light blue `0xFFE3F2FD`, content padding 16h/16v, input text
   `FontWeight.w500`.
6. Labels are identical for every field: 14px, bold, `Colors.black87`, 8px
   gap above the field. (The repo's `'Password..'` label has a stray double
   dot — keep the styling rule, not the typo.)
7. Buttons are ALWAYS elevated (pushed-up look): `ElevatedButton`,
   `elevation: 2`. Never flat, never outlined/bordered. The style: brand cyan
   background `0xFF4AC9E3`, dark grey text `0xFF353535`, radius 4, vertical
   padding 16, full width, uppercase bold 16px label with 0.5 letter spacing.
   While loading, the label swaps for a spinner inside the same button.
8. Credential fields: `enableSuggestions: false`, `autocorrect: false`;
   `autofillHints.username` / `autofillHints.password`.
9. Keyboard flow: `textInputAction.next` moves focus to the next field,
   `.done` submits. `resizeToAvoidBottomInset: false` with manual bottom
   padding from `MediaQuery.of(context).viewInsets.bottom`.
10. Customer ID is uppercased and trimmed on submit
    (`textCapitalization: TextCapitalization.characters` on the field).
11. Password visibility toggle uses outlined icons
    (`visibility_outlined` / `visibility_off_outlined`), `Colors.black54`,
    with Hide/Show password tooltip.
12. Loading never unmounts the form (preserves focus/state): fade the card
    with `Opacity`, block input with `IgnorePointer`, overlay a 48px
    `CircularProgressIndicator` in brand cyan.
13. Errors render as an inline banner under the fields: `red.shade50`
    background, `red.shade200` border, radius 8, 13px red text.
14. Screen-scoped logging via `AppLogger` with a `tag:` per screen
    (`info` for flow events, `dev` for values).
15. After every async gap: `if (!mounted) return`.
16. Dispose every `TextEditingController` and `FocusNode` in `dispose()`.

## Code sample
Distilled login form embodying the rules above:

```dart
class LoginFormCard extends StatefulWidget {
  const LoginFormCard({super.key});

  @override
  State<LoginFormCard> createState() => _LoginFormCardState();
}

class _LoginFormCardState extends State<LoginFormCard> {
  final _formKey = GlobalKey<FormState>();
  final _idController = TextEditingController();
  final _passwordController = TextEditingController();
  final _idFocusNode = FocusNode();
  final _passwordFocusNode = FocusNode();
  bool _isLoading = false;
  bool _isPasswordVisible = false;

  @override
  void dispose() {
    _idController.dispose();
    _passwordController.dispose();
    _idFocusNode.dispose();
    _passwordFocusNode.dispose();
    super.dispose();
  }

  static const _brandCyan = Color(0xFF4AC9E3);
  static const _darkGrey = Color(0xFF353535);

  InputDecoration _fieldDecoration() => InputDecoration(
        filled: true,
        fillColor: const Color(0xFFE3F2FD),
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: BorderSide(color: Colors.grey.shade300),
        ),
        // Same border enabled AND focused: no restyle on tap.
        enabledBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: _brandCyan, width: 2),
        ),
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(8),
          borderSide: const BorderSide(color: _brandCyan, width: 2),
        ),
        contentPadding:
            const EdgeInsets.symmetric(horizontal: 16, vertical: 16),
      );

  Widget _label(String text) => Text(
        text,
        style: const TextStyle(
          fontSize: 14,
          fontWeight: FontWeight.bold,
          color: Colors.black87,
        ),
      );

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: const EdgeInsets.all(24),
      decoration: BoxDecoration(
        color: Colors.white.withValues(alpha: 0.9),
        borderRadius: BorderRadius.circular(16),
        boxShadow: [
          BoxShadow(
            color: Colors.black.withValues(alpha: 0.1),
            blurRadius: 10,
            offset: const Offset(0, 4),
          ),
        ],
      ),
      child: Form(
        key: _formKey,
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          mainAxisSize: MainAxisSize.min,
          children: [
            _label('Customer ID'),
            const SizedBox(height: 8),
            TextFormField(
              controller: _idController,
              focusNode: _idFocusNode,
              textCapitalization: TextCapitalization.characters,
              enableSuggestions: false,
              autocorrect: false,
              autofillHints: const [AutofillHints.username],
              textInputAction: TextInputAction.next,
              onFieldSubmitted: (_) =>
                  FocusScope.of(context).requestFocus(_passwordFocusNode),
              style: const TextStyle(fontWeight: FontWeight.w500),
              decoration: _fieldDecoration(),
              validator: (v) =>
                  (v == null || v.isEmpty) ? 'Please enter your Customer ID' : null,
            ),
            const SizedBox(height: 20),
            _label('Password'),
            const SizedBox(height: 8),
            TextFormField(
              controller: _passwordController,
              focusNode: _passwordFocusNode,
              obscureText: !_isPasswordVisible,
              enableSuggestions: false,
              autocorrect: false,
              autofillHints: const [AutofillHints.password],
              textInputAction: TextInputAction.done,
              style: const TextStyle(fontWeight: FontWeight.w500),
              decoration: _fieldDecoration().copyWith(
                suffixIcon: IconButton(
                  icon: Icon(
                    _isPasswordVisible
                        ? Icons.visibility_outlined
                        : Icons.visibility_off_outlined,
                    color: Colors.black54,
                  ),
                  tooltip:
                      _isPasswordVisible ? 'Hide password' : 'Show password',
                  onPressed: () => setState(
                      () => _isPasswordVisible = !_isPasswordVisible),
                ),
              ),
              validator: (v) =>
                  (v == null || v.isEmpty) ? 'Please enter your Password' : null,
            ),
            const SizedBox(height: 24),
            SizedBox(
              width: double.infinity,
              child: ElevatedButton(
                onPressed: _isLoading ? null : () {},
                style: ElevatedButton.styleFrom(
                  backgroundColor: _brandCyan,
                  foregroundColor: _darkGrey,
                  padding: const EdgeInsets.symmetric(vertical: 16),
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.circular(4),
                  ),
                  elevation: 2,
                ),
                child: _isLoading
                    ? const SizedBox(
                        height: 20,
                        width: 20,
                        child: CircularProgressIndicator(
                          strokeWidth: 2,
                          valueColor:
                              AlwaysStoppedAnimation<Color>(_darkGrey),
                        ),
                      )
                    : const Text(
                        'LOGIN',
                        style: TextStyle(
                          fontSize: 16,
                          fontWeight: FontWeight.bold,
                          letterSpacing: 0.5,
                        ),
                      ),
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
- Keep `enabledBorder` and `focusedBorder` identical on every text field.
- Use the elevated cyan/dark-grey button everywhere — it is the app's button.
- Keep labels at 14/bold/black87 across all screens.

## Don't
- Never use flat (`TextButton`) or bordered (`OutlinedButton`) buttons.
- Never add a focus-highlight border color to text fields.
- Never unmount the form to show loading — fade + overlay instead.
- Never ship a solid-color auth background; brand image or glass theme.

## Notes for training pairs
- "Build a login screen with branded background image and centered card, my style"
- "Add a password field with visibility toggle following my text field conventions"
- "Convert this flat button to my button style" (input: flat button code)
- "Build a sign-up screen reusing my login page patterns"
