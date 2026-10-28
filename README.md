# Dilly Dally Activity Picker

A single-page picker for what to do next. It uses a typed or detected location, the day’s weather, mood, group size, and budget, then suggests nearby places plus a few at-home ideas.

Weather is from [Open-Meteo](https://open-meteo.com/). Places are from OpenStreetMap (Overpass, with Photon as a fallback). Search uses the Open-Meteo geocoder and Nominatim. Deal notes are typical patterns — always verify them.

**Live site:** https://avial0900.github.io/Activity-Picker/

The whole app is `index.html` (no build step). Open that HTTPS URL in Safari or Chrome. Location, weather, and places need a real `https://` page; opening the file directly (`file://`) blocks those requests on many phones, including iPhone.
