# Squawk ✈
### Your zenith, live.

Live flight tracker with three views — classic radar, world map, and 3D globe.

---

## Quick Start

### First time (you and friends)
1. Install Node.js from **https://nodejs.org** (LTS version)
2. Open Terminal, navigate here: `cd ~/Downloads/squawk`
3. Install: `npm install`
4. Launch: `npm start`
5. Open **http://localhost:3000** in Chrome

### Every time after
```
cd ~/Downloads/squawk
npm start
```
Then open http://localhost:3000

### Make it feel like an app
In Chrome: three dots menu → Save and share → Create shortcut → check "Open in new window"

---

## Features

### Three views
- **Radar** — classic green phosphor sweep, your local area
- **Map** — flat world map with live flight positions, zoom anywhere
- **Globe** — 3D rotating Earth, drag to spin, scroll to zoom

### Customization
- **Color themes** — Phosphor Green, Amber, Arctic Blue, Day Mode
- **Location** — search any ZIP code or city name to center the view
- **Range** — 25 to 500 nautical miles
- **Display toggles** — labels, altitude tags, trails, heading ticks, ground traffic
- **Altitude filter** — show only flights in a specific altitude band

### Tracking & sharing
- Track flights as "mine" (red) or a friend's (blue) with their name
- Alerts when a tracked flight enters range
- Generate a shareable link — friends open it and see your tracked flights highlighted
- Settings saved between sessions

---

## Sharing with friends

**Option 1 — Run locally (free, always works)**
Zip this folder and send it. They follow the Quick Start above.

**Option 2 — Host online (free public URL)**
Deploy to Vercel:
```
npm install -g vercel
vercel --prod
```
You get a URL like `squawk-yourname.vercel.app` that anyone can open.

---

## Customizing with Claude Code

Open Terminal in this folder, run `claude`, then just describe what you want:
- "Add a dark sidebar with neon pink accents"
- "Show flight speed as a color gradient on the globe"
- "Add a search box to find any flight by callsign"
- "Play a radar ping sound on the sweep"
- "Add weather radar overlay on the map"

---

## File structure
```
squawk/
  public/
    index.html    ← entire app (HTML + CSS + JS)
  package.json   ← run scripts
  CLAUDE.md      ← instructions for Claude Code
  README.md      ← this file
```

---

## Change default location
Open `public/index.html`, find near the top of the script:
```js
let CENTER_LAT = 42.7786, CENTER_LON = -71.2175;
```
Replace with your coordinates (right-click on Google Maps → "What's here?").
