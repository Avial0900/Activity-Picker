# Changelog

## 2026-10-28 — v1.2 "Any date"
- Pick any date (today or later) with a date picker, plus quick buttons for the next 7 days. "Plans for <date>" heading; the date is saved and goes back to today if the saved date has passed.
- Weather: Open-Meteo 16-day forecast for today to +15 days. Beyond that: "Typical for this date (not a forecast)" from the Open-Meteo historical archive (±3-day window, ~10 years: average high/low, % of days with ≥1 mm rain, average wind), used for nice/mixed/indoor too. If that fails: "Weather unknown" (outdoor options not filtered), with the reason shown and a Retry button.
- Weekday deal patterns follow the selected date's weekday; deal search links mention the date.
- Season awareness (Southern Hemisphere months flipped): beaches, outdoor pools and theme parks rank lower in cold months; golf and mini golf lower in winter; farms lower outside Jun–Nov and higher Sep–Oct.
- Simple OpenStreetMap opening-hours check flags "Likely closed that day"; hours it can't read are ignored.
- Renamed to "Dilly-Dally" (tab title and header), tagline "What should we do?".
- Overpass: smaller query (QL timeout 20 s, at most 800 results), plain GET request, 22 s / 12 s timeouts per server; Photon runs in parallel as backup.
- Photon: 2 queries at a time with 250 ms gaps, one retry with a 1.5–2.5 s backoff.
- Place results cached in localStorage per location + radius for 1 hour.
- Footer credits the sources actually used (Overpass or Photon/OpenStreetMap, Open-Meteo forecast or archive, Open-Meteo Geocoding/Nominatim).
- Code kept safe for older Safari (no optional chaining or `??`).
