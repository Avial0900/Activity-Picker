# Dilly-Dally

A single-page picker for what to do next. Pick any date (today or later), a typed or detected location, mood, group size, and budget. It uses that day’s weather, then shows three sections that load on their own, in this order:

1. **Places of interest** — nearby places, with a count and “Show more”. Museums, parks, libraries, and attractions rank above restaurants and bars (those stay on the list, underneath). The first five mix categories. The **foodie** mood puts food and drink back on top.
2. **Date night ideas** — tagged ideas (mood, indoor or outdoor, cost, and group size), a Shuffle button, and a directions link to the nearest matching real place.
3. **Happening on that date** — public holidays from [Nager.Date](https://date.nager.at/) (for example Thanksgiving), plus Find events links for Google, Eventbrite, Meetup, AllEvents, Ticketmaster, and Bandsintown (US), filled in with the date and mood.

Surprise me picks from all three. Ticketmaster’s own event list stays hidden unless `TICKETMASTER_KEY` at the top of the script is set. That key is empty here, so the page does not call the Ticketmaster API. The layout is a phone page: the viewport meta tag is set, and the sections stack in one column.

Weather is from [Open-Meteo](https://open-meteo.com/) — a 16-day forecast, or typical conditions from the historical archive after that. Places come from several sources in parallel, merged and de-duplicated, and show up as soon as any source answers: Photon (OpenStreetMap category lookups, 18 second timeout, one jittered retry), Wikipedia GeoSearch (English plus the local language), and Nominatim (bounded search, at most one request per second). Overpass is optional and runs in the background, so it never holds the list up. The search stops after 45 seconds and does not keep an empty or failed result. The location box starts empty — there is no default city — and asks “Where are we headed?” until a place is typed (Enter or Find looks it up) or “Use my location” is tapped. Search uses the Open-Meteo geocoder and falls back to Nominatim. Deal notes are typical patterns — always verify them. See [CHANGELOG.md](CHANGELOG.md).

**Live site:** https://avial0900.github.io/Dilly-Dally/ if the repository is renamed to Dilly-Dally, otherwise https://avial0900.github.io/Activity-Picker/.

The whole app is `index.html` (no build step). It does not use the repository name in any path, so either URL works after GitHub Pages is enabled. Open that HTTPS address in Safari or Chrome. Location, weather, and places need a real `https://` page; opening the file directly (`file://`) blocks those requests on many phones, including iPhone.
