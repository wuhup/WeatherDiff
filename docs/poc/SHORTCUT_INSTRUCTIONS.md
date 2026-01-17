# WeatherDiff iOS Shortcut POC

> **WeatherDiff** is an iOS Shortcut that tells you how today's weather compares to yesterday — showing temperature differences like "+5° warmer" instead of abstract absolute values like "18°C." The insight is simple: when you know it's colder than yesterday, you instantly remember what you wore and adjust accordingly, without doing mental math. The shortcut fetches feels-like temperature and rain probability from the free Open-Meteo API, compares the current hour to the same time yesterday, and sends a morning notification with the difference plus a rain warning if needed.

## Purpose

Validate the core hypothesis: "Relative temperature is more actionable than absolute temperature."

Use this shortcut daily for 1 week before building the app.

---

## Quick Start (Simplified Version)

### Step 1: Get Weather Data
1. Open **Shortcuts** app → Create new shortcut → Name it "WeatherDiff"
2. Add: **Get Current Location**
3. Add: **Get Contents of URL** with:

```
https://api.open-meteo.com/v1/forecast?latitude=[Latitude]&longitude=[Longitude]&hourly=apparent_temperature,precipitation_probability&daily=apparent_temperature_max,apparent_temperature_min&past_days=1&timezone=auto&forecast_days=1
```

Replace `[Latitude]` and `[Longitude]` with Location variables (tap → Select Variable → Current Location).

### Step 2: Parse Data
4. Add: **Get Dictionary from Input** → Save as `Data`
5. Add: **Get Dictionary Value** (Key: `hourly`) → Save as `Hourly`
6. Add: **Get Dictionary Value** from `Hourly` (Key: `apparent_temperature`) → Save as `Temps`

### Step 3: Get Current Hour Index
7. Add: **Get Current Date**
8. Add: **Format Date** → Custom Format: `H` → Save as `Hour`

### Step 4: Get Temperatures
9. Add: **Get Item from List** from `Temps` at index `Hour` → Save as `TempYesterday`
10. Add: **Calculate** → `Hour` + 24 → Save as `TodayIndex`
11. Add: **Get Item from List** from `Temps` at index `TodayIndex` → Save as `TempNow`

### Step 5: Calculate & Format Difference
12. Add: **Calculate** → `TempNow` - `TempYesterday` → Save as `Diff`
13. Add: **Round Number** → `Diff` to nearest integer → Save as `DiffRounded`

**Format with plus sign:**

14. Add: **If** → `DiffRounded` > 0
    - **Text**: `+[DiffRounded]` → Save as `FormattedDiff`
15. **Otherwise**
    - **Text**: `[DiffRounded]` → Save as `FormattedDiff`
16. **End If**

### Step 6: Show Result
17. Add: **Show Notification**
    - Title: `WeatherDiff`
    - Body: `[FormattedDiff]° vs yesterday`

---

## Adding Daily High/Low Comparison

The API URL above includes `&daily=apparent_temperature_max,apparent_temperature_min` which returns:

```json
"daily": {
  "time": ["2026-01-16", "2026-01-17"],
  "apparent_temperature_max": [8.3, 4.3],
  "apparent_temperature_min": [1.3, -1.3]
}
```

### Extract High/Low Diff

1. **Get Dictionary Value** from `Data` (Key: `daily`) → Save as `Daily`
2. **Get Dictionary Value** from `Daily` (Key: `apparent_temperature_max`) → Save as `Highs`
3. **Get Item from List** from `Highs` at index 1 → Save as `YesterdayHigh`
4. **Get Item from List** from `Highs` at index 2 → Save as `TodayHigh`
5. **Calculate** → `TodayHigh` - `YesterdayHigh` → Save as `HighDiff`
6. **Round Number** → `HighDiff`
7. Format with plus sign (same If/Otherwise pattern)

### Example Notification with High/Low

```
+2° vs yesterday right now
High: -4° vs yesterday
```

---

## Adding Rain Warning

After getting the temperature diff:

1. **Get Dictionary Value** from `Hourly` (Key: `precipitation_probability`) → Save as `RainProbs`
2. **Get Item from List** from `RainProbs` at index `TodayIndex` → Save as `RainProb`
3. Add: **If** → `RainProb` > 50
    - Include "☔ Rain likely" in notification
4. **Otherwise If** → `RainProb` > 30
    - Include "🌧 Possible rain" in notification
5. **End If**

---

## Automation Setup

To run automatically each morning:

1. Go to **Automations** tab in Shortcuts
2. Create **Personal Automation**
3. Select **Time of Day** → 7:00 AM (or your preferred time)
4. Add action: **Run Shortcut** → Select "WeatherDiff"
5. Toggle OFF "Ask Before Running"

---

## Validation Questions

After using for 1 week, answer:

1. Did the relative temperature help you decide what to wear? (Y/N)
2. Did you ever need to check the absolute temperature anyway? (Y/N)
3. Was the rain threshold (30%/50%) appropriate? (Y/N)
4. What time of day was most useful for the notification?
5. Would you pay $2 for an app version with a widget?

---

## Technical Notes

**API Response Structure:**
```json
{
  "hourly": {
    "time": ["2026-01-16T00:00", "2026-01-16T01:00", ...],
    "apparent_temperature": [2.3, 3.2, 2.8, ...],
    "precipitation_probability": [0, 0, 0, ...]
  },
  "daily": {
    "time": ["2026-01-16", "2026-01-17"],
    "apparent_temperature_max": [8.3, 4.3],
    "apparent_temperature_min": [1.3, -1.3]
  }
}
```

**Array indexing:**
- Hourly index 0-23: Yesterday (00:00 to 23:00)
- Hourly index 24-47: Today (00:00 to 23:00)
- Daily index 1: Yesterday
- Daily index 2: Today

**Timezone:**
- `timezone=auto` returns local time based on coordinates
