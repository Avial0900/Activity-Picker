# Changelog

## 2026-10-28 — v1.3.2
- Pressing Enter or tapping "Find activities" now uses what's typed in the location box: the top suggestion if one is showing, otherwise it looks the text up. Weather and places then reload together for that location. If nothing matches, it says "Couldn't find “…”".
- Weather and typical-weather results that arrive after the location has changed are thrown away, so two locations never mix.
- Place search counts as finished as soon as Photon is done. Overpass no longer holds anything up: it runs in the background, quietly merges in if it ever succeeds, and is skipped after 2 failures in a session.
- Location search: 400 ms debounce, Open-Meteo geocoding first; Nominatim only when Open-Meteo finds nothing, at most 1 request per second. Results are cached for the session.
- Fixed: locations ahead of the user's time zone (e.g. Tokyo) were stuck on "Loading weather…" because their forecast starts tomorrow. The view now moves to that location's first forecast day, without changing the saved date.
- Places now come from several sources at once, merged and de-duplicated; results appear as soon as any one of them answers:
  - Photon: 5 OpenStreetMap category lookups, 2 at a time, staggered, 18 s timeout, one jittered retry.
  - Wikipedia GeoSearch: English plus the local-language wiki, up to 10 km; notable places only, sorted into categories by title/description, with Wikipedia links.
  - Nominatim: 8 category searches inside the search area, at most 1 request per second, stops on HTTP 429/403.
  - Overpass: optional and never holds anything up.
- The "no places" error only appears if every source fails, and it names each one. A source still silent after 45 s is reported by name ("Photon: no response after 45s").
- No more Garden City default: the Garden City button is gone. On a first visit the location box is empty ("Type a city or address") with a friendly "Where are we headed?" prompt, and nothing loads until a location is picked. A location picked before is still remembered.
- Switching location resets the view to the saved date (or today), so a date moved forward for an ahead-of-time-zone city doesn't carry over to the next location.

## 2026-10-28 — v1.3.1 "Works anywhere, fast"
- Fixed: places never appeared on iPhone (live v1.2, Madison WI, 22 mi). v1.2 showed nothing until all 28 Photon keyword searches finished; each one could take up to 15 s plus a retry, so a slow or dropping phone connection meant several minutes on "Overpass unavailable — using Photon…" with only anywhere-ideas, and no error. English keywords ("museum", "park") also only matched names that fit them.
- Places now come from 5 Photon reverse lookups (osm_tag filters only, no keywords, nearest first, radius in km capped at 50, up to 50 results each), 2 at a time, staggered. This works for any language or script and isn't affected by the date line or the poles. Each group shows as soon as it arrives.
- Every request has a timeout (Photon 10 s, Overpass 8 s). Place loading stops after 45 s and shows whatever it has, with a message and a Retry button.
- Overpass failures no longer count as errors when Photon worked.
- Empty, failed or cut-short results are never cached. Zero places shows the reason plus "Try again, or a larger radius" with a Retry button.

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
