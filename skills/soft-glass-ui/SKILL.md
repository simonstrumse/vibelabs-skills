---
name: soft-glass-ui
description: Design system for creating premium glass UI using iOS 26 native Liquid Glass APIs (.glassEffect modifier) or fallback gradient-based approaches for older iOS versions. Use when building SwiftUI iOS apps with glassmorphism, premium UI, translucent cards, or modern Apple-style interfaces. Triggers on requests for liquid glass, glassmorphism, iOS 26 design, premium UI, or translucent interfaces.
---

# Glass UI Design System

A comprehensive design system for creating premium glass interfaces. Supports **iOS 26+ native Liquid Glass** (`glassEffect`) and provides **fallback patterns** for iOS 17-18.

## Critical Insight: iOS 26 Liquid Glass

**The "glass" effect in iOS 26 is NOT gradients and shadows.** It's a physically-accurate lensing system that samples the background and creates real-time refraction effects.

### What This Means:

1. **Glass needs colorful/dark backgrounds** - Unlike gradient-based glass that works on any background, native liquid glass samples what's behind it. On a plain white background, glass appears nearly invisible.

2. **Use `.ultraThinMaterial` + `.glassEffect()`** - The material provides the frosted base, the glassEffect adds the liquid refraction.

3. **Interactive feedback is built-in** - `.glassEffect(.regular.interactive())` provides touch scaling/bouncing animations automatically.

---

## iOS Version Strategy

| iOS Version | Approach |
|-------------|----------|
| **iOS 26+** | Native `.glassEffect()` modifier with `.ultraThinMaterial` |
| **iOS 17-18** | Gradient-based fallback with `.ultraThinMaterial` or custom gradients |

---

## iOS 26 Native Liquid Glass

### The Core Pattern

```swift
// The iOS 26 glass pattern
.background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 16))
.glassEffect(.regular, in: RoundedRectangle(cornerRadius: 16))
```

### Glass Effect Variants

| Variant | Usage |
|---------|-------|
| `.regular` | Standard glass for cards, containers |
| `.regular.tint(color)` | Adds color tint to the glass |
| `.regular.interactive()` | Enables touch feedback animations |
| `.clear` | More transparent, for media-rich backgrounds |

### Example: Glass Button

```swift
Button(action: action) {
    HStack {
        Image(systemName: "play.fill")
        Text("Start")
    }
    .padding()
    .foregroundStyle(.white)
    .background(LinearGradient.brandGradient)
    .clipShape(RoundedRectangle(cornerRadius: 16))
    .glassEffect(
        .regular.tint(.brandPrimary).interactive(),
        in: RoundedRectangle(cornerRadius: 16)
    )
}
```

### Background Requirements

**Glass needs something to sample.** Create rich backgrounds:

```swift
// Rich gradient background for glass to sample
struct BrandBackgroundModifier: ViewModifier {
    func body(content: Content) -> some View {
        content
            .background {
                ZStack {
                    // Deep base color
                    Color(hex: "0A0A1A")

                    // Animated gradient orbs
                    Circle()
                        .fill(RadialGradient(
                            colors: [.brandPrimary.opacity(0.4), .clear],
                            center: .center,
                            startRadius: 0,
                            endRadius: 200
                        ))
                        .frame(width: 400, height: 400)
                        .offset(x: -100, y: -150)

                    Circle()
                        .fill(RadialGradient(
                            colors: [.brandSecondary.opacity(0.3), .clear],
                            center: .center,
                            startRadius: 0,
                            endRadius: 180
                        ))
                        .frame(width: 360, height: 360)
                        .offset(x: 120, y: 200)
                }
                .ignoresSafeArea()
            }
    }
}
```

---

## Fallback for iOS 17-18

When targeting older iOS versions, use gradient-based glass:

```swift
struct GlassCardFallback<Content: View>: View {
    let content: Content

    var body: some View {
        content
            .padding(24)
            .background(
                LinearGradient(
                    colors: [.white, Color.surfaceWarm.opacity(0.3)],
                    startPoint: .top,
                    endPoint: .bottom
                )
            )
            .clipShape(RoundedRectangle(cornerRadius: 16))
            .shadow(color: .black.opacity(0.08), radius: 10, y: 4)
    }
}
```

### Availability Check Pattern

```swift
struct GlassCard<Content: View>: View {
    let content: Content

    var body: some View {
        if #available(iOS 26.0, *) {
            content
                .padding(24)
                .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 16))
                .glassEffect(.regular, in: RoundedRectangle(cornerRadius: 16))
        } else {
            content
                .padding(24)
                .background(.ultraThinMaterial)
                .clipShape(RoundedRectangle(cornerRadius: 16))
        }
    }
}
```

---

## Design Tokens

### Spacing Scale (8pt base)

```
xs: 4    s: 8    m: 16    l: 24    xl: 32    xxl: 48
```

### Corner Radius Scale

```
small: 8    medium: 12    large: 16    xl: 24
```

### Animation Timings

```
fast: 0.3s spring(response: 0.3, dampingFraction: 0.7)
standard: 0.5s spring
slow: 1.0s spring
stagger-delay: 0.05s per item
```

---

## Typography

Use **rounded font design** throughout:

```swift
.font(.system(size: 16, weight: .medium, design: .rounded))
```

| Style | Size | Weight |
|-------|------|--------|
| hero | 48-56 | bold/heavy |
| title | 28 | bold |
| headline | 20 | semibold |
| body | 16 | medium |
| caption | 14 | medium |
| small | 12 | regular |

---

## Color Strategy

### For iOS 26 Liquid Glass

Use **light text on dark backgrounds**:

```swift
extension Color {
    // Text (white-based for dark backgrounds)
    static var brandText: Color { .white }
    static var brandTextSecondary: Color { .white.opacity(0.7) }
    static var brandTextMuted: Color { .white.opacity(0.5) }

    // Accent colors (vibrant for glass tinting)
    static var brandPrimary: Color { Color(hex: "6366F1") }  // Indigo
    static var brandAccent: Color { Color(hex: "10B981") }   // Emerald
    static var brandSecondary: Color { Color(hex: "8B5CF6") } // Purple
}
```

### For Fallback (Light Mode)

Use **dark text on light backgrounds**:

```swift
extension Color {
    static var brandText: Color { Color(hex: "1E293B") }
    static var surfaceWarm: Color { Color(hex: "FDF8F3") }  // Warm cream
}
```

---

## Key Components

See [references/components.md](references/components.md) for detailed implementations:

- **GlassButton** - Primary actions with glass + gradient
- **GlassCard** - Container with glass background
- **GlassSelectionCard** - Selectable cards with tinted glass
- **GlassProgressRing** - Timer/progress with glass track
- **GlassToast** - Notification overlays
- **GlassIconCircle** - Icon badges with glass background

---

## Common Mistakes to Avoid

### 1. Using glass on white/plain backgrounds
Glass needs something to refract. Plain backgrounds make glass invisible.

### 2. Forgetting the shape parameter
`.glassEffect` requires a shape:
```swift
// Wrong
.glassEffect(.regular)

// Correct
.glassEffect(.regular, in: RoundedRectangle(cornerRadius: 16))
```

### 3. Using gradients instead of materials on iOS 26
The native glass effect looks better than any gradient approximation.

### 4. Not providing interactive feedback
Use `.interactive()` on buttons for native touch animations:
```swift
.glassEffect(.regular.interactive(), in: RoundedRectangle(cornerRadius: 16))
```

---

## Quick Reference

**iOS 26 Glass Card:**
```swift
.background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 16))
.glassEffect(.regular, in: RoundedRectangle(cornerRadius: 16))
```

**iOS 26 Tinted Glass:**
```swift
.glassEffect(.regular.tint(.brandPrimary), in: RoundedRectangle(cornerRadius: 16))
```

**iOS 26 Interactive Button:**
```swift
.glassEffect(.regular.tint(.accent).interactive(), in: RoundedRectangle(cornerRadius: 16))
```

**Deployment Target:** Set to iOS 26.0 to use native `.glassEffect()` API.
