# WeatherDiff Design Specification

> Design by Gemini, based on PRD brief

---

## 1. Strategic Design Rationale

### Strategic Alignment & Differentiation
The design ignores standard weather tropes (satellite maps, pressure districts). It focuses entirely on the **"Delta"** (the difference). This visual differentiation positions the app not as a meteorology tool, but as a **lifestyle utility**.

### Retention Strategy
The "sticky" feature is the Lock Screen widget. By designing the most useful widget in the store, we secure prime real estate on the user's phone, reducing churn.

### Value for Customer
We trade precision for decision. The user instantly knows "wear a jacket" versus "wear a t-shirt" without doing mental math.

### Accessibility as Market Expansion
The high-contrast, large-type design inherently supports accessibility and seniors, widening the total addressable market.

---

## 2. Visual Design Concept: "Semantic Temperature"

The visual language focuses on **Direction** and **Intensity**. We avoid photorealistic backgrounds. The UI is flat, bold, and typographic.

### A. Color System

A system that works for color-blind users by pairing color with iconography.

| State | Color Token (Light Mode) | Color Token (Dark Mode) | Meaning |
|-------|--------------------------|-------------------------|---------|
| Warmer | SF Red/Orange blend | System Orange | Heat, intensity, alert |
| Colder | SF Indigo/Blue blend | System Cyan | Cool, calm, layers needed |
| Same | System Gray 2 | System Gray 3 | Neutral, no change |
| Rain | System Blue | System Blue | Precipitation alert |
| Text | System Black | System White | Primary readability |

### B. Typography Hierarchy

**Font Family:** SF Pro Rounded

The rounded terminals feel friendlier and less "data-heavy" than standard SF Pro, matching the casual "what do I wear" use case.

| Element | Specification |
|---------|---------------|
| Hero Value (The Delta) | SF Pro Rounded, Heavy, 80pt+ (App) / Scaled (Widgets) |
| Primary Label | SF Pro Rounded, Semibold, Caps (e.g., "WARMER THAN YESTERDAY") |
| Secondary Data | SF Pro Text, Medium (e.g., "20% chance of rain") |

### C. Iconography (SF Symbols)

| State | Symbol |
|-------|--------|
| Warmer | `arrow.up` (filled variant for widgets) |
| Colder | `arrow.down` (filled variant for widgets) |
| Same | `equal` (custom weight to match arrows) |
| Rain | `cloud.drizzle.fill` or `umbrella.fill` |
| Wind (Optional) | `wind` |

---

## 3. Surface Designs & Layouts

### 1. Lock Screen Widget (Small - Circular)

**Layout:** A large, centered number with a small arrow next to it.

**Visual:**
- `+5` with an `arrow.up` glyph to the right
- If Same, display an `=` symbol
- No text labels — the user learns the position

### 2. Lock Screen Widget (Medium - Rectangular)

**Layout:** Two columns.

| Left Column | Right Column |
|-------------|--------------|
| Large Delta Value + Arrow (e.g., ↓ 4°) | Line 1: "Colder" |
| | Line 2: "Rain soon" OR "18°C" |

**Rain Logic (Smart Threshold):**

| Probability | Display | Rationale |
|-------------|---------|-----------|
| < 30% | Nothing | Noise reduction |
| 30% – 50% | Icon only | Awareness |
| > 50% | Icon + text warning | Action required |

No user configuration — adding a threshold setting creates cognitive friction that contradicts simplicity.

### 3. Home Screen Widget (Small)

**Background:** Dynamic color fill based on state (Soft Orange for Warmer, Soft Blue for Colder).

**Content:**
- Top Left: `umbrella.fill` (only if raining)
- Center: Large Delta Number (e.g., +7°)
- Bottom Center: "Warmer" (Small caps)

### 4. Home Screen Widget (Medium)

**Layout:** Split view.

| Left Side (60%) | Right Side (40%) |
|-----------------|------------------|
| "Current Moment" comparison | "Day Forecast" comparison |
| Big number, dynamic color background | Text: "Today's High" |
| | Value: +2° vs yesterday |

**Insight:** Answers both "How does it feel now?" and "Will it get hotter later?"

### 5. App Main Screen

**Layout: Stacked (No Toggle)**

The toggle interaction conflicts with glanceability. Users want the full picture immediately.

| Section | Height | Content |
|---------|--------|---------|
| Primary Hero | ~60% | "Current Moment" Delta (e.g., "5° Warmer right now") |
| Secondary Block | ~30% | "Day Forecast" Delta (e.g., "Highs will be similar today") |
| Footer | ~10% | Settings access + absolute temperature data |

**Visual Treatment:**
- Background: Subtle vertical gradient (Orange→White for warmer, Blue→White for colder)
- Primary hero: Massive central number with state label
- Secondary block: Smaller, clearly subordinate typography

**Why stacked wins:** Satisfies both "What do I wear now?" and "Will I regret this outfit later?" without requiring interaction.

### 6. Push Notification Design

#### A. Short Look (Banner)

Text-only, must convey delta instantly.

| Element | Content |
|---------|---------|
| **Title** | ↑ 5° Warmer than yesterday |
| **Body** | Rain likely around 5 PM. High of 18°C. |

Note: Use arrow glyph in title text for scanability.

#### B. Long Look (Expanded)

When user long-presses, load custom view mirroring Medium Widget layout.

**Canvas:** Rectangular (approx. height of 2 widgets)

**Layout:**

| Left Column | Right Column |
|-------------|--------------|
| Massive Delta Number + Arrow | Row 1: "Today's High: +2° vs yesterday" |
| Background: Warm/Cold state color | Row 2: "Rain: 60% chance" |

**Action Button:** "Open WeatherDiff"

**Technical Constraints:**
- Cache latest data in shared App Group (no spinner while fetching)
- Ensure Orange/Blue backgrounds have sufficient contrast for white text in translucent notification shade
- Test in both Light and Dark mode notification centers

---

### 7. First Launch / Onboarding Flow

**Visual Strategy: SF Symbols Compositions**

Custom illustrations feel "marketing-heavy" for a native iOS utility. Instead, compose SF Symbols into scenes using SwiftUI — scales perfectly, supports Dark Mode, matches system aesthetic.

| Screen | Copy | SF Symbols Composition |
|--------|------|------------------------|
| 1 | "Stop doing the math." | `person.fill` + `questionmark.circle` above head, beside `thermometer.sun` and `thermometer.snowflake` |
| 2 | "Know how it feels." | `tshirt.fill` next to large bold `arrow.up` |
| 3 | "Where are you?" | `map.fill` with `location.fill` overlay (Location Permission dialog) |
| 4 | "Morning briefing." | `bell.badge.fill` with `clock.fill` (Time picker for notification) |

**Why SF Symbols win:**
- Lightweight (no assets to download)
- Fully accessible (respects Dynamic Type)
- Feels distinctly "Apple"

---

## 4. Handling Special States & Formatting

### The "Same as Yesterday" State

- **Visual:** Distinct Gray/Monochrome palette
- **Icon:** An `=` sign or a flat horizontal bar `—`
- **Copy:** "Feels just like yesterday."

### The "Information Density" Tradeoff

**Solution: Progressive Disclosure**

| Level | Information | Surfaces |
|-------|-------------|----------|
| Level 1 (Glance) | Color + Arrow | All |
| Level 2 (Read) | The Number (by how much?) | All |
| Level 3 (Investigate) | Secondary text/icons (Rain? Absolute temp?) | Medium widgets, App |

On small widgets, we only show Levels 1 and 2.

### Numeric Formatting

To avoid confusion with absolute negative temperatures (e.g., is -5° the temperature or the difference?):

| State | App Format | Widget Format |
|-------|------------|---------------|
| Warmer | 5° Warmer | ↑ 5° |
| Colder | 5° Colder | ↓ 5° |

**Avoid:** Using just `-5°` or `+5°` without color/arrows, as `-5°` looks like absolute freezing temperature.

---

## 5. Interaction Patterns & Micro-animations

### Haptics

| Trigger | Haptic Type |
|---------|-------------|
| Weather data refreshes successfully | Success |
| Toggle between °C and °F in settings | Impact |

### Animations

**"The Scales":** On app load:
- If warmer: content slides slightly **up** and settles
- If colder: content slides slightly **down** and settles
- Reinforces the verticality of temperature

**Loading:** Pulsing opacity on the "Delta" number implies calculation is happening.

---

## 6. Accessibility Considerations

### VoiceOver

Ensure the screen reader announces the **meaning**, not just the glyphs.

| Bad | Good |
|-----|------|
| "Arrow up five degrees." | "Five degrees warmer than yesterday. Current absolute temperature is 18 degrees." |

### Dynamic Type

The "Delta" number is the hero. It must:
- Scale indefinitely but never truncate
- Stack vertically (rather than break horizontally) at huge text sizes

### Reduce Motion

Disable the vertical slide animation if the user has "Reduce Motion" enabled in iOS settings.

---

## Appendix: Design Tokens Summary

```swift
// Colors (SwiftUI)
extension Color {
    static let deltaWarmer = Color.orange
    static let deltaColder = Color.cyan
    static let deltaSame = Color.gray
    static let precipitation = Color.blue
}

// Typography
extension Font {
    static let deltaHero = Font.system(size: 80, weight: .heavy, design: .rounded)
    static let deltaLabel = Font.system(size: 14, weight: .semibold, design: .rounded).smallCaps()
    static let secondary = Font.system(size: 16, weight: .medium, design: .default)
}

// SF Symbols
enum DeltaIcon: String {
    case warmer = "arrow.up.circle.fill"
    case colder = "arrow.down.circle.fill"
    case same = "equal.circle.fill"
    case rain = "umbrella.fill"
}
```
