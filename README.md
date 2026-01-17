# WeatherDiff

📲 **[Install iOS Shortcut](https://www.icloud.com/shortcuts/4e00a6701187492ab96e49cb3fb35c39)**

Know if today is warmer or colder than yesterday — at a glance.

---

## The Problem

When you check the weather and see "18°C", your brain has to work:
- *What does that actually mean?*
- *Is that warm? Cold?*
- *What should I wear?*

## The Solution

**WeatherDiff** shows temperature as a *difference* from yesterday:

```
+5° warmer than yesterday
High: -3° colder
☔ Rain likely (65%)
```

When you know it's colder than yesterday, you instantly remember what you wore and adjust accordingly. No mental math required.

## How It Works

1. Fetches feels-like temperature from [Open-Meteo](https://open-meteo.com) (free, no API key)
2. Compares current hour to the same time yesterday
3. Shows the difference with rain probability

## Setup

1. **[Install the Shortcut](https://www.icloud.com/shortcuts/4e00a6701187492ab96e49cb3fb35c39)**
2. Run it manually, or set up a morning automation:
   - Shortcuts → Automations → Time of Day → 7:00 AM
   - Run Shortcut → WeatherDiff
   - Toggle OFF "Ask Before Running"

## Status

🧪 **POC Phase** — Validating the concept before building an iOS app with widgets.

## Background

Google's weather app used to have this feature — showing "5° colder than yesterday" — but quietly removed it years ago. No current weather app focuses on relative temperature. WeatherDiff fills that gap.

## Documentation

- [Product Requirements](docs/PRD.md)
- [Design Specification](docs/DESIGN.md)
- [Build Your Own Shortcut](docs/poc/SHORTCUT_INSTRUCTIONS.md)

## License

MIT — Free and open source.

---

*Weather data provided by [Open-Meteo](https://open-meteo.com)*
