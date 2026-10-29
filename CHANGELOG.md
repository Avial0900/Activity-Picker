# Changelog

## 2026-10-28 — v1.3 "Reliability pass"
- Based on the live v1.2 file (includes the site owner's fix: a cached place search returns before Photon starts).
- Photon runs one request at a time, most popular categories first (park, restaurant, café, museum, cinema…), with 300–420 ms of random spacing. Up to 2 retries with exponential backoff plus random jitter. Categories merged with several osm_tag filters per request: 21 queries, down from 28. Results appear as each query finishes.
- Overpass is opportunistic: one 8 s attempt on a single server, alternating servers between tries. It runs in parallel and never delays results. After 2 failures in a browser session it is skipped (sessionStorage). Overpass results replace matching Photon entries because they have more detail.
- Checked other no-key place sources: overpass.private.coffee timed out (20 s); maps.mail.ru Overpass allows the app's cross-site requests but returned 504. Neither added.
- Status shows "N places so far — still loading…" while results come in; finished results are cached for 1 hour.

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
