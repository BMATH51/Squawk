# Squawk — Claude Code Guide
### "Your zenith, live."

A live flight tracker with radar, map, and globe views. Everything lives in `public/index.html` — one file, no build step, no framework.

## Stack
- **Vanilla HTML/CSS/JS** — no framework
- **Leaflet.js** (CDN) — flat map view
- **Canvas API** — radar and globe drawn manually
- **OpenSky Network** — free real-time ADS-B flight data
- **Nominatim** (OpenStreetMap) — location search by ZIP/city
- **serve** (npm) — local web server

## Architecture

Everything is in `public/index.html`. Key sections:

### CSS (`:root` variables)
Four themes defined with CSS custom properties:
- Default (no attribute) = Phosphor Green
- `[data-theme="amber"]` = Amber
- `[data-theme="arctic"]` = Arctic Blue  
- `[data-theme="light"]` = Day Mode

To add a new theme: copy one of the `[data-theme="..."]` blocks and change the colors. Add a `.swatch` div in the HTML.

Key CSS variables:
- `--accent` — primary color (blips, text, sweep)
- `--bg`, `--bg2`, `--bg3` — background layers
- `--border`, `--border2` — border colors
- `--red` — "my flight" color
- `--blue` — "friend's flight" color
- `--text`, `--text2`, `--text3` — text hierarchy

### JavaScript — key variables
- `CENTER_LAT`, `CENTER_LON` — map/radar center point
- `rangeNM` — display radius in nautical miles
- `flights` — array of live flight objects `{callsign, lat, lon, alt, spd, hdg, sqk, onGround}`
- `trackedFlights` — object `{CALLSIGN: 'mine'|'friend'}`
- `friendNames` — object `{CALLSIGN: 'Name'}`
- `showLabels`, `showAltTags`, `showTrails`, `showHdg`, `showGround` — display toggles
- `altMin`, `altMax` — altitude filter range in feet

### Key functions
- `fetchFlights()` — fetches from OpenSky, falls back to demo data
- `getFiltered()` — returns filtered/sorted flight array for current view
- `flightColor(f)` — returns hex color for a flight (red/blue/accent)
- `showTooltip(f, px, py)` — shows flight info popup at pixel position
- `updateList()` — rebuilds the side panel flight list
- `renderTracked()` — rebuilds the tracked flights pills
- `drawRadar()` — canvas radar animation loop (requestAnimationFrame)
- `drawGlobe()` — canvas globe render (called from drawGlobeLoop)
- `updateMapMarkers()` — adds/removes Leaflet markers for map view
- `project3D(lat, lon)` — converts lat/lon to 3D globe coordinates
- `latLonToXY(lat, lon)` — converts lat/lon to radar canvas pixels

## Common customization requests

### "Add a new color theme"
1. Add a `[data-theme="name"]` block to the CSS `:root` section
2. Add a `.swatch.name` div in the themes section of the HTML
3. Add `.swatch.name { background: radial-gradient(...) }` to CSS
4. Add `data-theme="name"` to the swatch div

### "Change sweep speed"
Find `sweepAngle=(sweepAngle+0.011)` in `drawRadar()` — increase for faster.

### "Add sound on radar ping"
In `drawRadar()`, when `sweepAngle` crosses 0, use Web Audio API:
```js
if (sweepAngle < 0.015) {
  const actx = new AudioContext();
  const o = actx.createOscillator();
  const g = actx.createGain();
  o.connect(g); g.connect(actx.destination);
  o.frequency.value = 880; g.gain.setValueAtTime(0.1, actx.currentTime);
  g.gain.exponentialRampToValueAtTime(0.001, actx.currentTime + 0.3);
  o.start(); o.stop(actx.currentTime + 0.3);
}
```

### "Add weather overlay on map"
After `initMap()`, add a tile layer from OpenWeatherMap:
```js
L.tileLayer(`https://tile.openweathermap.org/map/precipitation_new/{z}/{x}/{y}.png?appid=YOUR_KEY`, {opacity:0.5}).addTo(leafMap);
```

### "Add flight search"
Add an input to the panel. On input, filter `flights` array by callsign containing the search term and highlight matching flight.

### "Show flight paths / routes as arcs on globe"
In `drawGlobe()`, use `project3D()` for origin and destination coords, draw a bezier curve between them using `gctx.bezierCurveTo()`.

### "Add altitude as color gradient"
In `drawRadar()` and `drawGlobe()`, instead of `flightColor(f)`, compute color based on `f.alt`: low = green, high = white/bright.

### "Make the globe auto-center on my location"
Set `globeRotY` and `globeRotX` based on `CENTER_LAT` and `CENTER_LON` on init.

## Testing
After any edit: refresh the browser (⌘R / Ctrl+R). No build step, no restart needed.
Only restart `npm start` if you change `package.json`.

## Do not change
- The Leaflet CDN URLs (they're version-locked for stability)
- The OpenSky API URL structure (bbox params are required)
- The `localStorage` key names (`squawk-tracked`, `squawk-names`, `squawk-theme`) — changing them loses user data
