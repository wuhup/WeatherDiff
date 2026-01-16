# WeatherDiff iOS Shortcut POC

## Purpose
Validate the core hypothesis: "Relative temperature is more actionable than absolute temperature."

Use this shortcut daily for 1 week before building the app.

---

## Create the Shortcut

### Step 1: Get Current Location
1. Open **Shortcuts** app
2. Create new shortcut, name it "WeatherDiff"
3. Add action: **Get Current Location**

### Step 2: Fetch Weather Data
Add action: **Get Contents of URL**

URL (tap and paste):
```
https://api.open-meteo.com/v1/forecast?latitude=[Latitude]&longitude=[Longitude]&hourly=apparent_temperature,precipitation_probability&past_days=1&timezone=auto&forecast_days=1
```

**Important:** Replace `[Latitude]` and `[Longitude]` with the Location variables from Step 1:
- Tap on `[Latitude]` → Select Variable → Current Location → Latitude
- Tap on `[Longitude]` → Select Variable → Current Location → Longitude

### Step 3: Parse the JSON
Add action: **Get Dictionary from Input**

### Step 4: Extract Current Hour Index
We need to find which array index corresponds to "now" and "yesterday same time."

Add action: **Get Dictionary Value**
- Key: `hourly`

Add action: **Get Dictionary Value** (from the hourly dict)
- Key: `time`

Add action: **Count**
- This gives us total hours in the array

The array has 48 hours (24 yesterday + 24 today). Current hour index = 24 + current_hour.

Add action: **Get Current Date**
Add action: **Format Date** → Custom Format: `H` (gives hour 0-23)
Add action: **Calculate** → Add 24 to the hour number

Save this as variable: `CurrentIndex`

Yesterday same hour: `CurrentIndex - 24`
Save as variable: `YesterdayIndex`

### Step 5: Get Temperature Values
Add action: **Get Dictionary Value**
- From the hourly dictionary
- Key: `apparent_temperature`

Add action: **Get Item from List**
- Index: `CurrentIndex`
- Save as: `TempNow`

Add action: **Get Item from List**
- Index: `YesterdayIndex`
- Save as: `TempYesterday`

### Step 6: Calculate Difference
Add action: **Calculate**
- `TempNow` - `TempYesterday`
- Save as: `TempDiff`

Add action: **Round Number**
- Round `TempDiff` to nearest integer
- Save as: `TempDiffRounded`

### Step 7: Get Rain Probability
Add action: **Get Dictionary Value**
- Key: `precipitation_probability`

Add action: **Get Item from List**
- Index: `CurrentIndex`
- Save as: `RainProb`

### Step 8: Build the Message
Add action: **If**
- `TempDiffRounded` > 0

**If true:**
Add action: **Text**
```
↑ [TempDiffRounded]° warmer than yesterday
```

**Otherwise (If ≤ 0):**

Add another **If** inside:
- `TempDiffRounded` < 0

**If true:**
Add action: **Text**
```
↓ [absolute value of TempDiffRounded]° colder than yesterday
```

**Otherwise:**
Add action: **Text**
```
Same as yesterday
```

**End If** (both)

Save result as: `MainMessage`

### Step 9: Add Rain Warning
Add action: **If**
- `RainProb` > 50

**If true:**
Add action: **Text**
```
[MainMessage]

☔ Rain likely ([RainProb]%)
```

**Otherwise if** `RainProb` > 30:
Add action: **Text**
```
[MainMessage]

🌧 Possible rain ([RainProb]%)
```

**Otherwise:**
Use `MainMessage` as-is

### Step 10: Show Notification
Add action: **Show Notification**
- Title: "WeatherDiff"
- Body: Final message from Step 9

---

## Simplified Version (if above is too complex)

For a quick test, create a simpler version:

1. **Get Contents of URL** (hardcode your home coordinates):
```
https://api.open-meteo.com/v1/forecast?latitude=YOUR_LAT&longitude=YOUR_LON&hourly=apparent_temperature,precipitation_probability&past_days=1&timezone=auto&forecast_days=1
```

2. **Get Dictionary from Input**

3. **Show Result** (just see the raw JSON first)

Then iterate from there.

---

## Automation Setup

To run this automatically each morning:

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
    "time": ["2024-01-15T00:00", "2024-01-15T01:00", ...],
    "apparent_temperature": [5.2, 4.8, 4.5, ...],
    "precipitation_probability": [0, 0, 10, ...]
  }
}
```

**Array indexing:**
- Index 0-23: Yesterday (00:00 to 23:00)
- Index 24-47: Today (00:00 to 23:00)
- Current hour's yesterday equivalent: `current_hour` (0-23)
- Current hour's today value: `current_hour + 24`

**Timezone:**
- `timezone=auto` returns local time based on coordinates
- All times in response are local
