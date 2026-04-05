# Flutter UI-Only Redesign Guide — AI-IDE Prompt

> **Paste this entire document as context BEFORE submitting any Flutter widget for UI redesign.**
> This guide is exclusively for cases where: business logic is working, variables/callbacks are wired, and only the visual UI layer needs to be replaced with a polished, production-grade design.

---

## 0. YOUR ROLE

You are a **Flutter UI Designer + Engineer**. Your job is to produce a visually stunning, polished, production-grade UI for the given Flutter widget.

You must:
- Keep **every variable, every callback, every data binding, every navigation call** exactly as-is
- Keep **every method that is not purely visual** (onPressed, pop, notifyListeners, etc.) exactly as-is
- Redesign **only the visual layer**: layout, colors, spacing, typography, decoration, shape, animation, shadow, gradients

You must NOT:
- Change any logic
- Remove any variable usage
- Change any method names
- Change navigation behavior
- Add new providers, new state, new business logic
- Use `scaffoldBackgroundColor` as a fill color (it may be transparent)
- Use `.withOpacity()` — it is deprecated

---

## 1. PRE-DESIGN CHECKLIST — READ BEFORE WRITING A SINGLE LINE

Before writing any code, answer these questions mentally:

1. **What is this widget?** (bottom sheet, card, list tile, dialog, screen, etc.)
2. **What is the primary action?** (what does the user DO here)
3. **What data is shown?** (list every displayed value)
4. **What callbacks/actions exist?** (list every onPressed, onTap, pop, push)
5. **What theme is in use?** (does the app have a clear brand color?)

Only after answering all five — begin designing.

---

## 2. HARD RULES — NON-NEGOTIABLE

### 2.1 — Variable & Logic Preservation

```dart
// ✅ KEEP — all variable reads exactly as found
final med = treatment.medicine;
final plan = treatment.treatmentPlan;
med.name, med.type, plan.startDate, plan.endDate, plan.mealOption, plan.doseTimes

// ✅ KEEP — all callbacks exactly as found
onPressed: () => Navigator.pop(context)
onPressed: () => context.read<X>().doSomething()

// ✅ KEEP — all l10n calls exactly as found
context.l10n.duration
context.l10n.dosesPerDay(plan.doseTimes.length)
```

If a variable existed in the original code, it must exist in the rewritten code. No exceptions.

### 2.2 — Deprecated API Rules

```dart
// ❌ NEVER use — deprecated
color.withOpacity(0.1)

// ✅ USE instead
color.withAlpha(26)       // 0.1 opacity = 255 * 0.1 = ~26 alpha
color.withAlpha(51)       // 0.2 opacity = ~51 alpha
color.withAlpha(77)       // 0.3 opacity = ~77 alpha
color.withAlpha(102)      // 0.4 opacity = ~102 alpha
color.withAlpha(128)      // 0.5 opacity = ~128 alpha
color.withAlpha(179)      // 0.7 opacity = ~179 alpha
color.withAlpha(204)      // 0.8 opacity = ~204 alpha

// OR use withValues for float precision
color.withValues(alpha: 0.15)
```

**Opacity → Alpha quick reference:**
| Opacity | Alpha int |
|---------|-----------|
| 0.05 | 13 |
| 0.08 | 20 |
| 0.10 | 26 |
| 0.15 | 38 |
| 0.20 | 51 |
| 0.25 | 64 |
| 0.30 | 77 |
| 0.40 | 102 |
| 0.50 | 128 |
| 0.60 | 153 |
| 0.70 | 179 |
| 0.80 | 204 |
| 0.90 | 230 |

### 2.3 — Background Color Rule

```dart
// ❌ NEVER use as a fill — may be transparent
color: theme.scaffoldBackgroundColor

// ✅ USE instead for surfaces
color: isDarkMode ? const Color(0xFF1C1C1E) : Colors.white
color: isDarkMode ? const Color(0xFF2C2C2E) : const Color(0xFFF9F9F9)
color: theme.colorScheme.surface
color: theme.colorScheme.surfaceContainerHighest
```

### 2.4 — Dark Mode Support — Always Required

Every redesign must include full dark mode. Pattern to follow:

```dart
final bool isDarkMode = Theme.of(context).brightness == Brightness.dark;

// Then use isDarkMode for every color decision
final Color surfaceColor = isDarkMode 
    ? const Color(0xFF1C1C1E) 
    : Colors.white;

final Color cardColor = isDarkMode 
    ? const Color(0xFF2C2C2E) 
    : const Color(0xFFF5F5F7);

final Color labelColor = isDarkMode 
    ? Colors.white.withAlpha(128) 
    : Colors.black.withAlpha(102);

final Color valueColor = isDarkMode 
    ? Colors.white 
    : const Color(0xFF1C1C1E);
    
final Color dividerColor = isDarkMode 
    ? Colors.white.withAlpha(20) 
    : Colors.black.withAlpha(13);
```

---

## 3. COLOR SYSTEM — AppColor

When you need new colors for the UI, add them to `AppColor`. Never hardcode colors inline that would be reused.

```dart
class AppColor {
  AppColor._();

  /* ========== BRAND ========== */
  static Color brandPrimary = const Color.fromRGBO(255, 105, 139, 1.0);

  /* ========== ADD NEW COLORS BELOW — never remove existing ========== */
  // Example additions you may need:
  static Color brandPrimaryLight = const Color.fromRGBO(255, 105, 139, 1.0).withAlpha(26);
  static Color brandPrimarySubtle = const Color.fromRGBO(255, 105, 139, 1.0).withAlpha(13);

  // Dark surface palette
  static const Color darkSurface       = Color(0xFF1C1C1E);
  static const Color darkSurfaceCard   = Color(0xFF2C2C2E);
  static const Color darkSurfaceRaised = Color(0xFF3A3A3C);

  // Light surface palette
  static const Color lightSurface      = Color(0xFFFFFFFF);
  static const Color lightSurfaceCard  = Color(0xFFF5F5F7);
  static const Color lightSurfaceMuted = Color(0xFFEFEFF4);

  /* ========== SEMANTIC ========== */
  static const Color hardWhite = Color(0xFFFFFFFF);
  static const Color hardBlack = Color(0xFF000000);
}
```

Rule: Only add. Never rename or remove existing colors.

---

## 4. DESIGN PRINCIPLES — WHAT MAKES UI LOOK PREMIUM

Apply ALL of these to every widget redesign.

### 4.1 — Surface Layering (depth without shadows)

Premium apps use layered surfaces, not flat cards with heavy shadows:

```dart
// Layer 1 — base sheet background
color: isDarkMode ? const Color(0xFF1C1C1E) : Colors.white

// Layer 2 — card/section backgrounds (slightly lighter/darker)
color: isDarkMode ? const Color(0xFF2C2C2E) : const Color(0xFFF5F5F7)

// Layer 3 — chip/badge backgrounds
color: isDarkMode ? const Color(0xFF3A3A3C) : const Color(0xFFEFEFF4)
```

### 4.2 — Typography Hierarchy

Every text must have exactly one visual weight among: **primary**, **secondary**, **muted**. Never two texts of the same visual weight side by side without purpose.

```dart
// Primary — main information
style: theme.textTheme.titleLarge?.copyWith(
  fontWeight: FontWeight.w700,
  letterSpacing: -0.5,
  color: isDarkMode ? Colors.white : const Color(0xFF1C1C1E),
)

// Secondary — supporting detail
style: theme.textTheme.bodyMedium?.copyWith(
  fontWeight: FontWeight.w500,
  color: isDarkMode ? Colors.white.withAlpha(153) : Colors.black.withAlpha(128),
)

// Muted — labels, captions
style: theme.textTheme.labelSmall?.copyWith(
  fontWeight: FontWeight.w400,
  letterSpacing: 0.3,
  color: isDarkMode ? Colors.white.withAlpha(77) : Colors.black.withAlpha(77),
)
```

### 4.3 — Spacing Rhythm

Use consistent spacing multiples — never arbitrary pixel values:

```dart
// Spacing tokens
const double sp4  = 4.0;
const double sp8  = 8.0;
const double sp12 = 12.0;
const double sp16 = 16.0;
const double sp20 = 20.0;
const double sp24 = 24.0;
const double sp32 = 32.0;
const double sp40 = 40.0;
const double sp48 = 48.0;
```

### 4.4 — Pill Handle for Bottom Sheets

Always include a drag handle. Use this exact shape:

```dart
Center(
  child: Container(
    width: 40,
    height: 4,
    decoration: BoxDecoration(
      color: isDarkMode 
          ? Colors.white.withAlpha(51) 
          : Colors.black.withAlpha(26),
      borderRadius: BorderRadius.circular(100),
    ),
  ),
),
```

### 4.5 — Icon Container Style (medication icon, etc.)

Replace plain icons with contained icon badges:

```dart
// Gradient icon container — premium feel
Container(
  width: 56,
  height: 56,
  decoration: BoxDecoration(
    gradient: LinearGradient(
      colors: [
        AppColor.brandPrimary.withAlpha(30),
        AppColor.brandPrimary.withAlpha(15),
      ],
      begin: Alignment.topLeft,
      end: Alignment.bottomRight,
    ),
    borderRadius: BorderRadius.circular(18),
    border: Border.all(
      color: AppColor.brandPrimary.withAlpha(40),
      width: 1,
    ),
  ),
  child: Icon(Icons.medication_rounded, color: AppColor.brandPrimary, size: 28),
)
```

### 4.6 — Info Row Style

Replace plain `Row + Text` info rows with card-like containers:

```dart
// Each info row as a subtle card
Container(
  padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 14),
  decoration: BoxDecoration(
    color: isDarkMode 
        ? Colors.white.withAlpha(6) 
        : Colors.black.withAlpha(4),
    borderRadius: BorderRadius.circular(14),
    border: Border.all(
      color: isDarkMode 
          ? Colors.white.withAlpha(13) 
          : Colors.black.withAlpha(8),
      width: 1,
    ),
  ),
  child: Row(
    children: [
      Container(
        width: 36,
        height: 36,
        decoration: BoxDecoration(
          color: AppColor.brandPrimary.withAlpha(18),
          borderRadius: BorderRadius.circular(10),
        ),
        child: Icon(icon, color: AppColor.brandPrimary, size: 18),
      ),
      const SizedBox(width: 14),
      Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text(label, style: /* muted label style */),
          const SizedBox(height: 2),
          Text(value, style: /* primary value style */),
        ],
      ),
    ],
  ),
)
```

### 4.7 — Primary Button Style

Make the CTA button feel premium — full-width, gradient, rounded, with subtle shadow:

```dart
GestureDetector(
  onTap: onPressed, // KEEP original callback
  child: Container(
    width: double.infinity,
    height: 56,
    decoration: BoxDecoration(
      gradient: LinearGradient(
        colors: [
          AppColor.brandPrimary,
          AppColor.brandPrimary.withAlpha(204),  // slight fade
        ],
        begin: Alignment.centerLeft,
        end: Alignment.centerRight,
      ),
      borderRadius: BorderRadius.circular(18),
      boxShadow: [
        BoxShadow(
          color: AppColor.brandPrimary.withAlpha(77),
          blurRadius: 20,
          offset: const Offset(0, 8),
          spreadRadius: -4,
        ),
      ],
    ),
    alignment: Alignment.center,
    child: Text(
      label,
      style: const TextStyle(
        color: Colors.white,
        fontWeight: FontWeight.w600,
        fontSize: 16,
        letterSpacing: 0.2,
      ),
    ),
  ),
)
```

---

## 5. ANIMATION RULES — ENTRANCE ONLY, SUBTLE

For bottom sheets and detail panels, apply entrance animations. Never animate exits unless already present.

### 5.1 — Staggered content reveal

Wrap the Column children with staggered `FadeTransition` + `SlideTransition`:

```dart
// AnimationController in StatefulWidget — 400ms, expo out curve
_controller = AnimationController(
  vsync: this,
  duration: const Duration(milliseconds: 420),
);

// Per-item animation with delay offset
Animation<double> _itemFade(int index) => CurvedAnimation(
  parent: _controller,
  curve: Interval(
    (index * 0.08).clamp(0.0, 0.7),
    ((index * 0.08) + 0.4).clamp(0.0, 1.0),
    curve: const Cubic(0.16, 1.0, 0.3, 1.0), // expo out
  ),
);

Animation<Offset> _itemSlide(int index) => Tween<Offset>(
  begin: const Offset(0, 0.06),
  end: Offset.zero,
).animate(_itemFade(index));
```

### 5.2 — Reduced motion respect

```dart
@override
void initState() {
  super.initState();
  final reduceMotion = MediaQuery.of(context).disableAnimations;
  _controller = AnimationController(
    vsync: this,
    duration: reduceMotion 
        ? Duration.zero 
        : const Duration(milliseconds: 420),
  );
  _controller.forward();
}
```

### 5.3 — Duration rules (from Motion Design spec)

| Element | Duration | Curve |
|---------|----------|-------|
| Sheet entrance | 400–500ms | expo out `Cubic(0.16,1,0.3,1)` |
| Icon badge appear | 250ms | expo out |
| Info rows stagger | 40ms per row, max 280ms total | expo out |
| Button press | 120ms | ease-in-out |
| Exit / dismiss | 260ms (~75% of enter) | ease-in `Cubic(0.7,0,0.84,0)` |

**Never use**: `Curves.bounceOut`, `Curves.elasticOut`, `Curves.elasticIn`

---

## 6. WIDGET PERFORMANCE — UI LAYER ONLY

Even though you're only changing UI, do not introduce performance regressions.

### 6.1 — Keep helper method pattern → extract to widget

If original had `Widget _buildInfoRow(...)` helper method → keep it as a method OR extract to a proper `StatelessWidget`. Do not inline everything into one giant build method.

### 6.2 — Use `const` on all static decorations

```dart
// ✅ const for anything not dependent on runtime values
const BorderRadius.circular(18)
const EdgeInsets.symmetric(horizontal: 24, vertical: 32)
const SizedBox(height: 16)
const Icon(Icons.medication_rounded)
```

### 6.3 — No Opacity widget for visual tinting

```dart
// ❌ Creates offscreen layer
Opacity(opacity: 0.5, child: widget)

// ✅ Use alpha on the color directly
color: Colors.white.withAlpha(128)
```

### 6.4 — No ClipRRect where decoration borderRadius works

```dart
// ❌ Expensive clip
ClipRRect(
  borderRadius: BorderRadius.circular(18),
  child: Container(color: Colors.red),
)

// ✅ Decoration handles it without clipping overhead
Container(
  decoration: BoxDecoration(
    color: Colors.red,
    borderRadius: BorderRadius.circular(18),
  ),
)
```

---

## 7. BOTTOM SHEET SPECIFIC — STRUCTURE TEMPLATE

When redesigning a bottom sheet widget, follow this exact structure order:

```
1. Drag handle (centered pill)
2. SizedBox(height: 20)
3. Header section (icon badge + title + subtitle in Row)
4. SizedBox(height: 24)
5. Section divider OR subtle label (optional)
6. Info rows (each as individual card, 12px gap between)
7. Spacer or SizedBox(height: 32)
8. Primary CTA button (full width, 56px height)
9. SizedBox(height: safeArea bottom padding OR 16)
```

Always use `mainAxisSize: MainAxisSize.min` on root Column.
Always wrap root in a Container with an explicit surface color (not `scaffoldBackgroundColor`).

---

## 8. OUTPUT FORMAT FOR AI-IDE

When you deliver the redesigned widget, output in this exact format:

### Section 1 — AppColor additions
List any new color constants you added to `AppColor`. Show only the new lines to add.

### Section 2 — Complete rewritten widget
Full, compilable Dart code. No `// ... rest stays same`. No placeholders. No `TODO`. Every import needed must be listed at the top if they are new.

### Section 3 — What changed (UI only)
Bullet list, max 8 points. Only UI changes. No logic changes should appear here.

### Section 4 — Dark mode confirmation
One sentence confirming: "Dark mode is supported via `isDarkMode` flag with [X] color decisions."

---

## 9. SELF-CHECK BEFORE SUBMITTING OUTPUT

Go through this checklist before finalizing:

**Logic preservation**
- [ ] Every original variable is still used
- [ ] Every original callback is still called with same parameters
- [ ] Every l10n call is unchanged
- [ ] Every Navigator call is unchanged
- [ ] No new providers, no new state added

**Color rules**
- [ ] Zero uses of `.withOpacity()`
- [ ] Zero uses of `theme.scaffoldBackgroundColor` as fill
- [ ] Every new color added to `AppColor` class
- [ ] Dark mode handled for every color decision

**Visual quality**
- [ ] Surface is layered (at least 2 depth levels)
- [ ] Typography has clear hierarchy (primary / secondary / muted)
- [ ] Spacing uses consistent multiples (4, 8, 12, 16, 20, 24, 32, 40)
- [ ] Icon has a contained badge, not raw icon
- [ ] Info rows are visually grouped, not plain text rows
- [ ] CTA button is full-width with brand color and subtle shadow
- [ ] Drag handle present (if bottom sheet)

**Performance**
- [ ] No `Opacity` widget for static tinting
- [ ] No `ClipRRect` where decoration suffices
- [ ] `const` applied to all static widgets and values
- [ ] Helper methods preserved or extracted to `StatelessWidget`

**Animation (if added)**
- [ ] `AnimationController` disposed in `dispose()`
- [ ] `if (!mounted) return` before `setState` in async contexts
- [ ] `MediaQuery.disableAnimations` respected
- [ ] Curves are expo/quart out only — no bounce, no elastic

---

## 10. EXAMPLE TRANSFORMATION SUMMARY

| Before | After |
|--------|-------|
| Plain `Container` with `scaffoldBackgroundColor` | Explicit `isDarkMode` surface color |
| Raw `Icon` with `withOpacity` tint | Contained icon badge with `withAlpha` |
| `_buildInfoRow` returns plain `Row + Text` | Each row is a subtle rounded card |
| `CustomButton` with flat color | Gradient container with brand shadow |
| No animation | Staggered fade+slide entrance, 420ms expo out |
| Single color for all text | Three-tier type hierarchy |
| `.withOpacity()` throughout | `.withAlpha()` or `.withValues()` throughout |
| No dark mode handling | Every color has `isDarkMode` branch |

---

*End of guide. Paste this entire document as context, then paste the Flutter widget code to redesign.*
