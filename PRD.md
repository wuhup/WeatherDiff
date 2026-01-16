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

## MVP Scope

### Philosophy: Widget-First, Minimal App

The **widget IS the product**. The app exists primarily for:
- Granting location permissions
- Configuring notification times
- Displaying the same info as the widget (for consistency)

### Information Hierarchy (Critical)

All surfaces (widget, app, notification) follow this priority:

| Priority | Information | Example | User Need |
|----------|-------------|---------|-----------|
| **1st** | Temperature difference | "+7° warmer" / "-5° colder" / "Same as yesterday" | Core decision: dress warmer or cooler? |
| **2nd** | Precipitation indicator | Rain expected / No rain | Secondary decision: bring umbrella? |
| **3rd** | Absolute temperature | "Currently 22°C" | Context for those who want it |
| **4th** | Additional details | Wind, humidity | Deep-dive info (app only) |

### Comparison Types

Both comparisons shown:
1. **Right now vs. yesterday same time** — "Right now it's 5° warmer than yesterday at this time"
2. **Today's high/low vs. yesterday's** — "Today's high will be 3° cooler than yesterday"

---

## Features (MVP)

### 1. Lock Screen Widget
- **Sizes:** Small (circular) and medium (rectangular)
- **Content:** Temperature difference, precipitation indicator (medium only)
- **Tap action:** Opens app

### 2. Home Screen Widget
- **Sizes:** Small, Medium
- **Content:** Temperature difference (both sizes), precipitation indicator + absolute temp (medium)

### 3. App (Minimal)
- **Main screen:** Same info as medium widget, slightly expanded
- **Settings:**
  - Notification time picker (e.g., 7:00 AM daily)
  - Temperature unit toggle (°C / °F)
  - About/credits

### 4. Push Notifications
- **User-scheduled** (e.g., every morning at 7 AM)
- **Content:** "Today is 6° warmer than yesterday. No rain expected."
- **Rich notification:** Shows the same visual as the widget

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
- **Data needed:**
  - Current feels-like temperature (apparent_temperature)
  - Yesterday's feels-like temperature (same hour)
  - Today's forecast high/low (feels-like)
  - Yesterday's actual high/low (feels-like)
  - Precipitation probability
- **Note:** All comparisons use feels-like temperature, not actual temperature

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

## Timeline Estimate

| Phase | Scope |
|-------|-------|
| **Week 1** | Core data layer + basic app UI |
| **Week 2** | Widgets (lock screen + home screen) |
| **Week 3** | Notifications + polish |
| **Week 4** | TestFlight beta + iteration |

---

## Appendix: Competitive Landscape

| App | Relative Temp Feature | Notes |
|-----|----------------------|-------|
| Apple Weather | ❌ | Shows hourly/daily but no "vs yesterday" |
| Google Weather | ❌ (removed) | Had it years ago, removed |
| Carrot Weather | ❌ | Personality-driven, not comparison-driven |
| Weather Underground | ❌ | Data-rich but no relative comparison |

**WeatherDiff fills a gap no current weather app addresses.**
