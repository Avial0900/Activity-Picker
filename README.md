# Dilly-Dally

A single-page picker for what to do next. Pick any date (today or later), a typed or detected location, mood, group size, and budget. It uses that day’s weather, then suggests nearby places plus a few at-home ideas.

Weather is from [Open-Meteo](https://open-meteo.com/) — a 16-day forecast, or typical conditions from the historical archive after that. Places are from OpenStreetMap (Overpass, with Photon as a fallback). Search uses the Open-Meteo geocoder and Nominatim. Deal notes are typical patterns — always verify them. See [CHANGELOG.md](CHANGELOG.md).

**Live site:** https://avial0900.github.io/Dilly-Dally/ if the repository is renamed to Dilly-Dally, otherwise https://avial0900.github.io/Activity-Picker/.

The whole app is `index.html` (no build step). It does not use the repository name in any path, so either URL works after GitHub Pages is enabled. Open that HTTPS address in Safari or Chrome. Location, weather, and places need a real `https://` page; opening the file directly (`file://`) blocks those requests on many phones, including iPhone.
