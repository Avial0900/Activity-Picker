# Changelog

## v1.14.2 — 2026-10-30
- Added Impact site-verification meta tag in <head> (Fever affiliate program). Meta tag only, no script.

## v1.14.1 — 2026-10-30 (built on v1.14)
- **No direct browser fallbacks when our relay answers with an upstream error:** Overpass and Nominatim calls go straight to `overpass-api.de` / `overpass.kumi.systems` / `nominatim.openstreetmap.org` only if the Worker route is missing (HTTP 403/404/405) or the Worker itself can't be reached. A Worker 502/429/timeout means the public server is down or limiting too, so the call now just fails quietly. Before, it was retried from the browser, which failed CORS in Safari, logged console errors and added up to ~8 s per call. Places still fill from Photon, Wikipedia and Nominatim while Overpass is down.
- **MET Norway only through the Worker:** the direct `api.met.no` fallback was removed (MET requires an identifying User-Agent, which browsers can't send, and it failed CORS in Safari). NWS is unchanged for US forecasts.
- **"City, ST" share links:** a two-letter US state abbreviation at the end of a place name (e.g. `?q=Garden City, MI`) with US coordinates is now read as the state, not as a country code. Holidays use `US` (not `MI`), and the US-only Eventbrite/Meetup/Bandsintown links work again.
- **Holiday lookup is silent when it fails:** a date.nager.at outage no longer shows a red error and Retry button on the Events tab. Holidays simply don't appear; `state.ev.holErr` is kept for debugging.

## v1.14 — 2026-10-29
- **Places and Activities dividers:** with moods picked, matching items come first, then a thin divider: "Other things nearby" / "Other ideas", or "No places (ideas) found that match that vibe (those vibes), here are some others:" when nothing matches. Counts and "Show more" still work; there is no divider without moods.
- **Deeper place vibes:** historic buildings, plaques and memorials are now nerdy (primary) and chill, never adventurous. New OSM types, still in the single Overpass query through the Worker /osm route:
  - climbing, trampoline, kayak/boat rental, go-karts, axe throwing, zip line/ropes
  - tea rooms, bookshops, board-game shops/cafés, dessert
  - science centers, observatories, makerspaces
  - bakeries, food halls, wineries/cider (ranked below attractions)
  - dance studios, paint-and-sip/pottery studios
  
  With moods picked, the result pool can hold up to 8 matching items per type (up from 4).
- **Idea abundance:** 110 new ideas (coloring books, karaoke out and at home, thrift shopping and more), all with honest mood profiles. All 197 ideas are in the pool with or without a mood (the no-mood list now differs from v1.13 but keeps the at-home slotting, weather/season rules and the varied top 5). Every vibe now has 33+ ideas. The Viator rules are unchanged; name-only links were added for the cooking class and the food tour.
- **"Social" means other people around:** social was removed from 35 ideas a couple or family does on its own (batting cages, golf range, language app race, home dance party, escape room, bowling, arcade, card/board/video game nights, rooftop drinks, etc.). It stays on pickup games, pickleball, fun runs, karaoke, trivia, game cafés, group classes, festivals, open mics, volunteering, hosting, and dance classes.
- **Weather on "Let the weather decide":** nice weather now gives outdoor ideas only a mild boost (+1.5); indoor ideas no longer drop below the vibe divider. Bad weather still demotes outdoor ideas; explicit Indoor/Outdoor settings and Places ranking are unchanged.
- **22 more group ideas** (run club, group hike, library class, dance social, pub quiz, board game meetup, pottery class, garden volunteer day, Friendsgiving, holiday market, watch party, bonfire, tailgate, group bike ride, cookie swap, craft night, museum members' night, choir/drum circle, bowling league, ugly-sweater party, white-elephant swap); 219 ideas total.
- **Places dedupe:** near-duplicate names within ~150 m collapse into one card (normalized names of 6+ characters, edit distance ≤ 2, e.g. "Urban Kyaks" vs "Urban Kayaks"); the record with more tags is kept.
- **Free-events loader:** uses `bbox_unpadded` expanded by the radius (with the cos(lat) correction) when present and valid, otherwise the padded bbox. Works with the slimmer region files (no id/address/region/country/cost/source fields).
- **Find activities starts the search (results no longer auto-update):** editing any field (Where, including picking a suggestion, "Use my location" or the ✕ clear button; date strip or date picker; moods; people; budget; radius; setting) no longer rebuilds results. Results are built from a snapshot of the form taken when "Find activities" is tapped (or Enter in Where); late weather/places/events fetches fill in that search only. First load without URL params shows no results ("Fill in what you can, then tap Find activities"; a saved place is pre-filled). Share links with params still auto-run once.
  - After a search, changed settings highlight the button and show "Settings changed. Tap Find activities to update the results."; the cue clears when settings match the results again or after Find.
  - Find shows "⏳ Finding…" (disabled) until results render, then scrolls to the results if they're below the fold. Find with unchanged settings re-shuffles ideas and re-runs. Empty Where shows an inline message; the ✕ button clears only the field and keeps the current results.

## v1.13 — 2026-10-29
- **Moods really change results (all three tabs):** every place type, idea, Ticketmaster genre and free-event category now has a short mood profile: one primary mood plus up to 2 secondary ones (`CAT_PROF`, `IDEA_PROF`, `TM_MOOD`, `FREE_CAT`; the `CATS`/`ANYWHERE` arrays are unchanged). Scoring: primary +5, secondary +2, and a bonus when an item matches several of the selected moods. Items that match no selected mood get −6 and always come after every matching item, including in the top-5 diversity pass, the at-home idea slotting and the events source balancing. The "why" line names the vibe in plain words (e.g. "cozy: warm and low-key"). Markets count as foodie (primary). Rankings with no mood selected are unchanged.
- **Events divider:** with moods selected, matching events come first, then a thin divider ("Other things happening that day", or "No <moods> events found that day; here are some others:" when nothing matches), then the rest in normal order. "Show more" is unchanged; no divider without moods.
- **Free-event `vibes`:** when an event has a `vibes` array, its valid mood ids (at most 3, unknown ones ignored) are used as its moods: the first is primary, the rest secondary. `[]` marks a practical service (util). The adults/21+/seniors and kids→never-romantic filters and `FREE_UTIL` still apply. Events without `vibes` keep the inferred moods, capped at 3.
- **Regional free-events feed:** `loadFree()` now reads `./data/free-events/index.json` and fetches, in parallel, only the region files whose padded bbox intersects the search circle (radius converted to degrees, with a cos(lat) correction) and whose `date_range` covers the date. Each region is cached by path, and a region that fails is skipped. The index is validated: `files` must be an array, and each path must match `^free-events/[a-z0-9-]+\.json$`. If the index is missing or invalid, it falls back to the legacy `./data/free-events.json`. No region fetches when nothing matches (e.g. Paris).
- **Mobile:** `.row>div{min-width:0;max-width:100%}` and `select{max-width:100%}`, so the "Let the weather decide" select can't widen the page at 375/320px.

## v1.12 — 2026-10-29
- **Tab/heading rename:** second tab is now "🚲☕💕 Activities/Date night" (wraps cleanly on mobile via `<wbr>`/`&nbsp;`; `.tab` wraps at ≤520px). Panel heading renamed "💡 Date night ideas" → "💡 Things to do together". `?tab=date` still opens this tab.
- **Ideas 57 → 87:** 30 new things to do together — planetarium show, botanical garden/conservatory, lookouts, sunsets, drive-in, roller skating, trampoline park, pick-your-own, thrift/antique hunt, bookstore crawl, live music, gallery hop, kite flying, sledding; seasonal (snow fort, leaf pile, holiday lights, backyard campout, s'mores); at home (card games, bake cookies, indoor picnic, movie marathon, dance party, LEGO build-off, paint-and-sip, homemade pizza, scavenger hunt, charades/Pictionary, two-person book club).
- Ideas now carry season/weather/at-home tags (`IDEA_OPT`, `ideaFit`): snow ideas only with snow/freezing winter weather, clear-sky ideas hidden in rain, at-home picks boosted in rough weather, southern-hemisphere seasons flipped. First view interleaves at-home and out-and-about ideas (every 3rd slot; every 2nd when it's rainy). Cards show "At home".
- **Places:** planetariums are now found and ranked (tier 1, museum-like). Viator links for "Planetarium show" and planetarium places map to museum searches; still name-only, never on gardens, lookouts, at-home ideas, movies, food, events, markets, libraries or free stuff.
- **Desktop background:** soft landscape illustration (`bg-landscape.webp`, `.jpg` fallback, 1280×720) as a fixed `body::before` layer, only at ≥900px (mobile never downloads it). Opacity **0.40** in light mode, 0.12 + dimmed in dark mode; cards remain solid; no layout shift.

## v1.11 — 2026-10-29

### Added
- **SEO and sharing (requested by DD Mark):**
  - Title "Dilly-Dally — What should we do?" and a meta description.
  - Canonical URL https://letsdillydally.com/.
  - Open Graph and Twitter card tags, with a new `og-image.png` (1200×630, about 50 KB).
  - WebApplication JSON-LD: free, no ratings.
  - `robots.txt` and `sitemap.xml` covering /, /privacy and the 5 landing pages.
  - IndexNow key file `7ff6c1c1b865ab3d35b3acb87ff1aa3a.txt`.
- **Shareable links:**
  - Format: `?q=&lat=&lon=&date=&mood=a,b&budget=0-3&setting=auto|indoor|outdoor|any&tab=places|date|events`.
  - Params are validated, and invalid values are ignored. Characters that could inject HTML are stripped from `q`. Valid params override saved settings.
  - With no params, behavior is unchanged (no default city).
  - New 🔗 Share button, shown once a location is set. It uses `navigator.share`, falls back to copying the link to the clipboard (with a "Link copied" message), and as a last resort shows a selectable link field.
  - Coordinates are rounded to 3 decimals, even for "Use my location".
  - When the page was opened from a shared link (or after Share is used), the address bar stays in sync via `history.replaceState`, so no history entries are added.
- **Static landing pages** (crawlable without JS; each has a preset button into the app):
  - /detroit-rainy-day
  - /detroit-date-night
  - /detroit-free-this-weekend (the button computes the coming Saturday when the page loads and on click)
  - /royal-oak-date-night
  - /ann-arbor-with-kids
  - A subtle "Explore" line in the footer links to them.

### Changed
- Museum Viator links now require a museum/gallery-type word in the name, or a tourism=museum tag plus a Wikipedia/Wikidata reference or a website. This drops tag-only entries such as "Heritage Hall" and "Legacy Beacon" in Detroit.

## v1.10 — 2026-10-29

### Changed
- Viator links still appear only on suggestion names (plus the small "via Viator ↗" label). There are no separate Viator lines; matching is now wider.
  - Museums: OSM `tourism=museum`, plus galleries or attractions whose name is museum-like. They link to "museum tour {city}" (`dd-museum`). Entries whose name ends in "Building" or "House" are skipped.
  - New name patterns: bike tours/rentals (`dd-biketour`), ghost/haunted tours (`dd-ghosttour`), history/heritage tours (`dd-historytour`), kayak/canoe/paddleboard (`dd-kayak`), day trips (`dd-daytrip`).
  - Landmark rule widened: monastery, abbey, aqueduct, ruins and historic site now count; apartments, stores, shops and farms are excluded.
  - Date night ideas now linked: Museum wander → museum tour, Bike ride → bike tour, Scenic drive → day trip.
  - New ideas: "Ghost or history walking tour", "Kayak or paddleboard outing", "Sunset or river cruise". Each one's name is its link.
  - The `VIATOR` config now maps each type to [search words, campaign] in one table.
  - Tooltip now reads "Search tours & experiences for … in {city} on Viator", so it doesn't imply a ticket is needed (e.g. free museums).
  - Still never linked: events, the free feed, holidays, markets, parks, nature areas, trails, gardens, libraries, restaurants, cafés, bars, playgrounds, beaches.

## v1.9 — 2026-10-29

### Added
- Viator affiliate links. On bookable experiences, the suggestion's name itself links to a Viator search for similar activities in the chosen city. There's no separate button and no pop-up.
  - It opens in a new tab with `rel="sponsored noopener"`, next to a small muted "via Viator ↗" label. The tooltip says "Search similar … options in <city> on Viator", so it doesn't promise an exact match.
  - Date night ideas that get links: paint night (painting class), escape room (escape game), pottery/craft workshop (pottery class), try a new cuisine and dessert crawl (food tour), brewery/wine tasting (wine tasting), and neighborhood photo walk (walking tour).
  - Places that get links: escape rooms, landmark-type sightseeing attractions (palace, castle, monument, fountain, tower, cathedral, temple, …, but not buildings, houses, centers or parks) as "things to do", and any place named like a boat tour or cruise, food tour, cooking class or walking tour.
  - Never linked: parks, nature, gardens, trails, libraries, restaurants, cafés, bars, markets, farms, playgrounds, dog parks, beaches, events (free, Ticketmaster, the DD Eyes And Ears feed), and holidays.
  - Existing Maps, Website/Wikipedia, OSM, Find deals and Groupon links stay as secondary links.
  - All settings live in one `VIATOR` constant: base URL, pid, mcid, medium, category maps, and the optional Detroit destination page. Campaign codes are `dd-<category>`. There's no API, no key and no tracker.
- Footer note now reads: "Some links are affiliate links (e.g. Viator); Dilly-Dally may earn a commission at no extra cost to you."

## v1.8.1 — 2026-10-29

### Fixed
- Far-date weather (more than 10 days out) sometimes showed no weather line or stayed stuck on "Loading weather…" when the date was changed while the NWS/MET forecast was still loading, when the date was picked before a location, after rapid date changes, or after clearing the location mid-load.
  - Weather now has a single `ensureWx()` path. It runs on every date change, as soon as a location is set (the history lookup no longer waits for NWS/MET to finish), and when the forecast load completes.
  - Forecast loads carry a generation token (`wxGen`), so a stale load from an older location never overwrites a newer one. Clearing the location invalidates any load in flight.
  - Stale history responses are stored under their own location|date key and only redraw if they still match the current selection.
  - "Loading…" only appears while a request is actually in flight. Otherwise the line always ends in a forecast, "Typical for this date", or "Weather unknown" with Retry. History lookups also have a hard 20s watchdog.

### Changed
- Privacy page link enabled in the footer (`SHOW_PRIVACY_LINK=true`), now that privacy.html is approved (effective October 29, 2026).

## v1.8 — 2026-10-29 (weather/geocoding sources, footer, privacy draft)
- **Open-Meteo removed everywhere.** Its free tier is non-commercial.
- **Forecast, US:** the National Weather Service (api.weather.gov `/points` → `forecast` + `forecastHourly`), called straight from the browser (CORS `*`, no key). It covers about 7 days.
- **Forecast, worldwide and beyond NWS:** MET Norway Locationforecast 2.0 compact, through the Worker's `/weather/met`. The Worker sets an identifying User-Agent, rounds coordinates to 4 decimals and caches 30–60 min (respecting Expires). It covers about 9–10 days. For US locations, MET fills the days NWS doesn't cover. If NWS fails, MET covers everything.
- **Dates past the forecast:** the Visual Crossing Timeline through the Worker's `/weather/history` (secret `VC_KEY`). Each call is 1 record (`include=days` with minimal elements). Climate-stats answers are cached 14 days and real long-range forecasts 6 h. Without a key the route returns a JSON 503 "not configured", and the app shows "Forecast not available this far out yet" without filtering outdoor options.
- **Weather mapping:** NWS `shortForecast` and MET `symbol_code` are mapped onto the existing weather codes, so the nice/mixed/indoor, rough-weather and best-outdoor-window logic is unchanged. MET has no rain probability in compact, so it is estimated from the symbol and the precipitation amount.
- **Location search:** Photon (Komoot) first, then Nominatim (1 req/s, kept). Local-script and diacritic names work (São Paulo, Kraków, Ōsaka, Wānaka).
- **Worker caching for Nominatim and Overpass:** `/osm/nominatim/{search,reverse,lookup}` (24 h) and `/osm/overpass` (1 h, two mirrors, 12 s per mirror). The app goes through them when the Worker has them, and calls the services directly if the proxy errors.
- **Every weather source fails soft and loads independently.** The Places, Date night and Events tabs never wait on weather.
- **Footer:** OpenStreetMap copyright; NWS, MET Norway (CC BY 4.0) and Visual Crossing; Wikipedia; Ticketmaster; the free-events source; the commission note.
  - The Privacy link is hidden behind `SHOW_PRIVACY_LINK=false` until `privacy.html` ships.
  - No tips or donations button (Alex decided against one).
  - No analytics script or affiliate links were added.
- **`privacy.html`:** a draft, delivered separately and not linked yet.
- **Worker:** the CORS allowlist is unchanged. `deploy.sh` now also sets `VC_KEY` from env when present. **The Worker has not been redeployed.**

## v1.7 — 2026-10-29
- Results are now one card with tabs: 📍 Places of interest, 💕 Date night, 🎉 Events. Only the selected section shows, so no more scrolling past one section to reach the next.
  - Each tab shows a live count and a small spinner while it is still loading. The Events tab gets a dot when there are vibe-matched events for the date.
  - On phones (≤520px) the labels shorten to "Places / Date night / Events". The tab bar stays pinned at the top while you scroll through results.
  - Accessible: role=tablist/tab/tabpanel, aria-selected, aria-controls, and ←/→/Home/End keys. Swiping left/right on touch switches tabs; vertical scrolling still works.
  - The last tab you used is remembered (localStorage dd_tab), defaulting to Places.
  - "Surprise me" switches to the tab that holds its pick.
  - Each section's content, Show more, markets, holidays, free events and Ticketmaster work as before, and each still loads on its own.
- ✕ clear button inside the "Where" box (42×40px tap target, aria-label "Clear location"). It only shows when the box has text. Tapping it:
  - empties the box and the saved location (dd_loc), so a reload stays empty, and closes the suggestions
  - cancels place, weather, event, market and pending "Use my location" loads, so the old city's results never pop back in
  - resets everything to the empty state and puts the cursor in the box

## v1.6 — 2026-10-29
- Live Ticketmaster events for everyone, through a Cloudflare Worker proxy (worker/, "dilly-dally-events"), so the key stays server-side.
  EVENTS_PROXY_URL = https://dilly-dally-events.dilly-dally.workers.dev/events
- Ticketmaster order: the user's own pasted key (called directly) > built-in TICKETMASTER_KEY (empty) > proxy > hidden.
- Proxy errors show a short "temporarily unavailable" note; 429 shows the rate-limit message. Free badge, 401 message and vibe matching are unchanged.
- Ticketmaster and free-feed events are now merged by vibe score, with at most 3 of either in the first 5, so ticketed events are no longer buried under library listings.
- Requests are capped at 50 events.
- Worker: whitelisted params, radius ≤ 100, size ≤ 50, CORS only for avial0900.github.io and localhost/127.0.0.1, 15-min edge cache keyed without the key, JSON errors.

## v1.5.1 — 2026-10-28
- Public holidays: markets (open and unverified) and places with listed hours but no PH rule show "🎌 Holiday — hours may differ".
- Free events feed (data/free-events.json, 1,336 events): fetched after a 0.7s delay, off the critical path (15s timeout).
  Indexed once, so filtering takes about 1-3 ms per date change.
- Multi-day events (e.g. light displays) show on every day of their range with "runs through <date>"; midnight placeholder start times are hidden.
- Vibe matching hardened against noisy keyword tags: tags only count when the title agrees (e.g. "Computer Basics" is no longer
  "musical"/"romantic"); a category is re-checked against the title (e.g. "How To Use Libby" is not a concert, chess is not a sport);
  "outdoors" is ignored for indoor events; adult-only titles lose "family".
- Practical library services (tutoring, resume, Medicare, computer basics, ESL…) rank last and don't match vibes.
- Exact duplicate listings (same title, time and venue) are collapsed; the events list is capped at 5 with Show more.

## v1.5 — 2026-10-28
- 🧺 Markets open that day (in the events section): OSM marketplaces and farmers markets from Photon, Nominatim (with opening hours)
  and Overpass when it responds. Missing hours are filled in with one Nominatim lookup. Hard timeouts (25s total), never blocks
  other sections, never caches empty results.
- Built-in opening_hours parser (no 1.3 MB library): month/date ranges (e.g. "May-Oct Sa 07:30-12:20", "Apr 15-Nov 15: ..."),
  weekday ranges, nth weekday (Sa[1]), PH (uses the holiday data), off/closed, 24/7. Shows the hours for that day.
  Unparseable or missing hours are listed under "Hours unverified". Closed markets are only counted.
- Markets are free and family-friendly in vibe matching, and appear in Surprise me.
- Ticketmaster: keeps the built-in TICKETMASTER_KEY, plus a "⚙️ Ticketmaster key" setting at the bottom of the events section
  (stored in localStorage, with a link to the free registration). "Free" badge for $0 events; clear 401/429 messages.
- Free local events feed: reads ./data/free-events.json (5s timeout, cache-bust) and shows events within the radius on the chosen date with a
  Free badge, vibe-matched via category and tags, deduped against OSM markets. Missing or invalid file is ignored silently.
  Test file: data/free-events.sample.json (fake SAMPLE events, not shipped as free-events.json).

## v1.4.1 — 2026-10-28
- Places of interest ranking now uses category tiers that outrank mood and distance:
  1) museums, parks, libraries, galleries, gardens, nature, zoos/aquariums, historic sites/landmarks, viewpoints, trails, beaches, theaters, cinemas;
  2) other activities; 3) food & drink (restaurants, cafés, bars, breweries), still listed but below.
- New "foodie" mood chip: selecting it moves food & drink to the top.
- The first 5 places shown are diversified (1 per category first, then up to 2).
- Wikipedia classification fixes: pubs and city/town articles are no longer tagged as gardens/parks; statues are landmarks.
- Verified libraries come from Photon (amenity:library), Nominatim ("library") and Wikipedia.

## v1.4 — 2026-10-28
- Three sections in fixed order, each with a header, count, independent loading, 5 items + "Show more":
  📍 Places of interest · 💡 Date night ideas · 🎉 Happening on <date>.
- Date night ideas: 56 ideas tagged by mood/indoor-outdoor/cost/group size, filtered by mood, weather and budget,
  randomized with a 🔀 Shuffle button, each tied to the nearest matching real place with a Google Maps directions link.
- Events: public holidays on/near the selected date via Nager.Date (no key, CORS OK), e.g. Thanksgiving, Culture Day.
- "Find events" deep links for the date + mood keywords: Google, Eventbrite, Meetup, AllEvents, Ticketmaster, Bandsintown (US).
- Optional Ticketmaster Discovery API: set TICKETMASTER_KEY at the top of the script; hidden when empty.
  Events get mood/indoor/cost tags, image, time, venue, distance, price and a ticket link.
- "Surprise me" now picks from places, ideas and events.
- Geolocation now records the country code (used for holidays).
- Wikidata festivals evaluated and skipped (slow, no reliable dates).

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

