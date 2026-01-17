# WeatherDiff

📲 **[Install iOS Shortcut](https://www.icloud.com/shortcuts/4e00a6701187492ab96e49cb3fb35c39)**

**WeatherDiff** is an iOS Shortcut (and future app) that tells you how today's weather compares to yesterday — showing temperature differences like "+5° warmer" instead of abstract absolute values like "18°C." The insight is simple: when you know it's colder than yesterday, you instantly remember what you wore and adjust accordingly, without doing mental math. The shortcut fetches feels-like temperature and rain probability from the free Open-Meteo API, compares the current hour to the same time yesterday, and sends a morning notification with the difference plus a rain warning if needed. This serves as a proof-of-concept to validate the core hypothesis before building a full iOS app with widgets.

## Status

🧪 **POC Phase** — Validating the concept with an iOS Shortcut before building the app.

## Documentation

- [Product Requirements Document](docs/PRD.md)
- [Design Specification](docs/DESIGN.md)
- [Shortcut Instructions](docs/poc/SHORTCUT_INSTRUCTIONS.md)

## Core Idea

Google's weather app used to show "5° colder than yesterday" — a feature that was quietly removed years ago. For many people, this relative comparison is far more actionable than absolute temperatures:

| Absolute | Relative |
|----------|----------|
| "It's 18°C" → *What does that mean? What should I wear?* | "+5° warmer than yesterday" → *I was fine yesterday, I can wear less today* |

## License

MIT — Free and open source.
