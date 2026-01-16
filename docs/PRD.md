# WeatherDiff - Product Requirements Document

## Problem Statement

When checking the weather, users see absolute temperatures (e.g., "18°C") that require mental effort to interpret. Questions arise: *What does that actually mean? What should I wear?*

**The insight:** Relative temperature is cognitively easier. "5° colder than yesterday" immediately triggers memory: *I was cold yesterday, I need a warmer jacket today.* Google's weather app had this feature years ago but quietly removed it.

## Solution

**WeatherDiff** — an iOS app that shows temperature *difference* compared to yesterday as the primary information, with absolute temperature as secondary context.

## Target User

People who:
- Check weather daily to decide what to wear
- Find absolute temperatures hard to intuit
- Want glanceable weather info without opening an app
- Value simplicity over feature-rich weather dashboards

## Core Value Proposition

> "Know if today is warmer or colder than yesterday — at a glance."

---

## MVP Scope (Radically Simplified)

### Philosophy: Widget-First, Minimal App

The **widget IS the product**. The app exists only for:
- Granting location permissions
- Configuring notification time
- Instructions to add widget

### Information Hierarchy (Critical)

| Priority | Information | Example | User Need |
|----------|-------------|---------|-----------|
| **1st** | Temperature difference | "↑ 5° warmer" / "↓ 3° colder" / "Same as yesterday" | Core decision: dress warmer or cooler? |
| **2nd** | Precipitation indicator | Rain icon if >30%, icon+text if >50% | Secondary decision: bring umbrella? |
| **3rd** | Absolute temperature | "18°C" | Context for those who want it |

### Comparison Type (MVP)

**Single comparison only:**
- **Right now vs. yesterday same time** — "5° warmer than this time yesterday"

Daily high/low comparison: **deleted from MVP** (add post-validation if users request it)

---

## Features (MVP)

### 1. Home Screen Widget (Single Size)
- **Size:** Medium (most information density)
- **Content:** Temperature difference + precipitation indicator + absolute temp
- **Tap action:** Opens app

### 2. App (Permissions + Settings Only)
- **Main screen:**
  - Current weather diff (same as widget)
  - "Add Widget" instructions with visual guide
- **Settings:**
  - Notification time picker
  - Temperature unit toggle (°C / °F)
  - About/credits

### 3. Daily Notification (Single)
- **User-scheduled** (e.g., 7:00 AM)
- **Content:** "↑ 5° warmer than yesterday. Rain likely."
- **No rich notification in MVP** — text only

### Deleted from MVP
- Lock screen widgets (add later)
- Multiple widget sizes (add later)
- Daily high/low comparison (validate need first)
- Rich notifications (text is sufficient for validation)

---

## User Flows

### First Launch
1. Open app → Welcome screen explaining the concept
2. Request location permission (required)
3. Request notification permission (optional but encouraged)
4. Set notification time (if permitted)
5. Show main screen + prompt to add widget

### Daily Use
1. **Primary:** Glance at lock screen/home screen widget
2. **Secondary:** Receive morning notification
3. **Tertiary:** Open app for more detail (rare)

---

## Technical Requirements

### Platform
- iOS 17+ (for latest widget capabilities)
- Swift / SwiftUI
- WidgetKit for widgets

### Weather Data
- **API:** Open-Meteo (free, open source, no API key required)
- **Endpoint:** `/v1/forecast` with `past_days=1`
- **Verified data availability:**
  - `apparent_temperature` — hourly feels-like (confirmed)
  - `precipitation_probability` — hourly rain % (confirmed)
  - `past_days=1` — returns yesterday's hourly data in same response (confirmed)
  - `timezone=auto` — returns local time based on coordinates (confirmed)
- **Note:** All comparisons use feels-like temperature, not actual temperature

**Example API call:**
```
https://api.open-meteo.com/v1/forecast?latitude={lat}&longitude={lon}&hourly=apparent_temperature,precipitation_probability&past_days=1&timezone=auto&forecast_days=1
```

### Location
- Current location only (no saved locations in MVP)
- Background location updates for widget refresh
- Graceful degradation if location unavailable

### Data Refresh
- Widgets: iOS-managed refresh (~15-60 min intervals)
- App: Refresh on open
- Notifications: Fetch fresh data before sending

---

## Design Brief for Gemini

> **Instructions:** Copy this entire section as a prompt to Gemini for UI/UX design work. Do not prescribe solutions — let Gemini explore and propose.

---

### DESIGN BRIEF: WeatherDiff iOS App

**Project Summary:**
WeatherDiff is an iOS weather app with a unique value proposition: it shows temperature *relative to yesterday* as the primary information, rather than absolute temperature. The insight is that "5° colder than yesterday" is cognitively easier to act on than "18°C" — users can remember what they wore yesterday and adjust accordingly.

**Target Platform:**
- iOS 17+
- iPhone (primary)
- Lock screen widgets, home screen widgets, notifications, minimal app

**Target User:**
- Daily weather checkers who want to know "what to wear"
- People who find absolute temperatures hard to intuit
- Users who prefer glanceable information over detailed weather dashboards
- Values simplicity and quick decision-making

**Core User Need:**
"Help me instantly understand if I need to dress warmer or cooler than yesterday, and if I need an umbrella."

**Information to Display (in priority order):**

| Priority | Information | User Question Answered |
|----------|-------------|------------------------|
| 1 (Primary) | Temperature difference vs yesterday | "Is it warmer or colder?" |
| 2 (Secondary) | Precipitation indicator | "Do I need an umbrella?" |
| 3 (Tertiary) | Absolute temperature / details | "What's the actual temp?" (for those who want it) |

**Comparison Types to Show:**
1. Current moment vs same time yesterday (e.g., "Right now: 5° warmer than yesterday at this time")
2. Today's forecasted high/low vs yesterday's actual high/low (e.g., "Today's high: 3° cooler than yesterday")

**Special States to Design For:**
- Temperature is same as yesterday → Display "Same as yesterday"
- Warmer than yesterday → Positive difference
- Colder than yesterday → Negative difference
- Location unavailable → Graceful error state
- Data loading → Loading state
- Notification permission denied → Prompt state

**Surfaces to Design:**

1. **Lock Screen Widget (Small - Circular)**
   - Most constrained space
   - Must communicate warm/cold difference at a glance
   - Tap opens app

2. **Lock Screen Widget (Medium - Rectangular)**
   - Can include precipitation indicator
   - Tap opens app

3. **Home Screen Widget (Small)**
   - Glanceable difference information

4. **Home Screen Widget (Medium)**
   - Difference + umbrella indicator + secondary info

5. **App Main Screen**
   - Fuller version of widget information
   - Both comparison types (current + daily high/low)
   - Access to settings

6. **App Settings Screen**
   - Notification time picker
   - Temperature unit toggle (°C / °F)
   - About/credits

7. **Push Notification**
   - Rich notification with visual similar to widget
   - Text content: difference + precipitation summary

8. **First Launch / Onboarding**
   - Explain the concept (why relative temp matters)
   - Location permission request
   - Notification permission request
   - Notification time selection
   - Widget installation prompt

**Design Constraints:**
- Must work in iOS light and dark modes
- Must be accessible (VoiceOver, Dynamic Type)
- Widget refresh is iOS-managed (data may be 15-60 min old)
- No internet = no data (weather API dependent)

**Design Questions to Explore:**
- How to visually encode "warmer" vs "colder" at a glance?
- How prominent should the precipitation indicator be relative to temperature?
- How to handle the information density tradeoff on smaller widgets?
- What's the right visual treatment for "Same as yesterday"?
- How to make the app feel native to iOS while being distinctive?
- What's the optimal onboarding flow to explain the concept quickly?
- How should the numerical difference be formatted? (+7° vs 7° warmer vs ↑7°)

**Reference Context:**
- Google Weather used to have this feature (removed years ago)
- No current weather app focuses on relative temperature
- The app should feel like a natural iOS utility, not a flashy weather app

**Deliverables Requested:**
1. Visual design exploration for all surfaces listed above
2. Interaction patterns and micro-animations (if any)
3. Iconography recommendations (custom vs SF Symbols)
4. Color system that works for warm/cold/neutral states
5. Typography hierarchy
6. Dark mode adaptations
7. Accessibility considerations

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Widget adoption | >80% of users add widget within first week |
| Daily widget views | Proxy via app opens (should be LOW if widget works) |
| Notification opt-in | >60% enable notifications |
| Retention (7-day) | >50% |

---

## Out of Scope (MVP)

- Multiple saved locations
- Weather trends/charts
- Hourly breakdown
- Severe weather alerts
- Apple Watch app
- iPad-specific layouts
- Customizable widget themes
- Social sharing

---

## Design Decisions (Resolved)

1. **Zero difference display** — Show "Same as yesterday" (friendly text, no number)
2. **Temperature basis** — Use **feels-like temperature** for all comparisons (more relevant for "what to wear" decisions)
3. **Night mode** — Deferred to design phase
4. **First-day problem** — On first launch (no yesterday data), show absolute temp with message: "Check back tomorrow for comparison"
5. **Rain threshold** — Smart fixed logic (no user setting):
   - <30%: Don't show
   - 30-50%: Icon only
   - >50%: Icon + text warning

---

## Future Considerations (Post-MVP)

- Apple Watch complication
- Multiple locations
- "This time last week/month/year" comparisons
- Outfit suggestions based on historical patterns
- Siri shortcuts integration
- Weather trends visualization

---

## Monetization

**Free + Open Source**
- No ads, no subscriptions, no in-app purchases
- Source code published on GitHub
- Community contributions welcome

---

## Validation (Pre-Build)

### iOS Shortcut POC
Before building the app, validate the core hypothesis with an iOS Shortcut:

**Location:** `poc/SHORTCUT_INSTRUCTIONS.md`

**Validation questions after 1 week:**
1. Did relative temperature help you decide what to wear?
2. Did you ever need to check absolute temperature anyway?
3. Was the rain threshold (30%/50%) appropriate?
4. What time was most useful for the notification?
5. Would you pay $2 for an app version with a widget?

**Go/No-Go criteria:**
- If answers to #1 and #5 are "Yes" → Build the app
- If answer to #2 is consistently "Yes" → Reconsider information hierarchy

---

## Timeline Estimate

| Phase | Scope |
|-------|-------|
| **Week 0** | Shortcut POC validation (1 week of daily use) |
| **Week 1** | Core data layer + app UI (permissions, settings, instructions) |
| **Week 2** | Home screen widget (medium) + notification |
| **Week 3** | Polish + TestFlight beta |

---

## Appendix: Competitive Landscape

| App | Relative Temp Feature | Notes |
|-----|----------------------|-------|
| Apple Weather | ❌ | Shows hourly/daily but no "vs yesterday" |
| Google Weather | ❌ (removed) | Had it years ago, removed |
| Carrot Weather | ❌ | Personality-driven, not comparison-driven |
| Weather Underground | ❌ | Data-rich but no relative comparison |

**WeatherDiff fills a gap no current weather app addresses.**
