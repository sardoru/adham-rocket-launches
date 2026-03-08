# Adham's Rocket Launch Tracker

A 3D interactive globe that visualizes every rocket launch — past, upcoming, and live satellite constellations orbiting Earth in real time.

Built as an educational tool for a middle schooler who loves NASA and SpaceX.

**Live site:** [adham-rocket-launches.vercel.app](https://adham-rocket-launches.vercel.app)

## Features

### 3D Globe
- Interactive Earth with blue marble texture, topographic bumps, atmosphere glow, and starfield background
- Smooth rotation, zoom, and pan controls
- Launch sites rendered as pulsing rings on the surface
- Animated trajectory arcs from launch pads toward orbit targets

### Live Satellite Constellations
- **6,000+ Starlink satellites** — every active satellite displayed with orbital trails
- **ISS & Tiangong** — highlighted with larger markers and full 92-minute orbit paths
- **GPS (USA)** — 32 satellites at 20,200 km altitude with 6-hour orbital paths
- **GLONASS (Russia)** — Russian navigation constellation
- **Galileo (EU)** — European navigation constellation
- **OneWeb** — LEO broadband constellation
- **Iridium NEXT** — 66 communication satellites
- **Science satellites** — Hubble Space Telescope and others

All satellite positions are computed using **SGP4 orbital propagation** from real Two-Line Element (TLE) data. Positions update every 3 seconds; orbital paths refresh every 30 seconds.

Toggle any constellation on/off from the control panel (top-left).

### Rocket Launch Data
- **Upcoming launches** with countdown timers (T-Xd Xh)
- **Past launches** with success/failure status
- Filter by provider: SpaceX, NASA, ULA, Roscosmos
- Search by mission name, rocket, or location
- Click any launch to zoom the globe and see trajectory arc + details

### Launch Detail Panel
- Rocket name, launch date/time, pad, location, orbit, mission type
- Random educational fun facts about rockets and space

### Design
- Space-themed dark UI with twinkling CSS stars
- Color-coded providers: SpaceX (blue), NASA (orange), ULA (teal), others (purple)
- Responsive layout for desktop, tablet, and mobile
- Orbitron + Space Grotesk typography

## Architecture

Single-file static site — no build step, no backend, no API keys.

```
index.html    — Complete application (HTML + CSS + JS, ~1,600 lines)
og-image.png  — Open Graph preview image (1200x630)
```

### Dependencies (CDN)

| Library | Version | Purpose |
|---------|---------|---------|
| [Three.js](https://threejs.org/) | 0.160.0 | 3D rendering, particle systems, line geometry |
| [Globe.gl](https://globe.gl/) | 2.35.1 | 3D globe visualization (built on Three.js) |
| [satellite.js](https://github.com/shashwatak/satellite-js) | 5.0.0 | SGP4/SDP4 orbital propagation from TLE data |

### Data Sources

| Source | Endpoint | Data |
|--------|----------|------|
| [The Space Devs Launch Library 2](https://thespacedevs.com/) | `ll.thespacedevs.com/2.2.0` | Past & upcoming rocket launches (free, no key) |
| [CelesTrak](https://celestrak.org/) | `celestrak.org/NORAD/elements/gp.php` | Two-Line Element sets for all satellite constellations (free, no key) |

Fallback launch data is embedded in case the Launch Library API is unreachable.

### Performance

Rendering 6,000+ satellites efficiently uses direct Three.js particle systems rather than individual globe.gl objects:

- **Satellite dots**: One `THREE.Points` (BufferGeometry) per constellation — single GPU draw call regardless of count
- **Orbital paths**: One `THREE.LineSegments` (BufferGeometry) per constellation — all paths in a single draw call
- **SGP4 propagation**: ~6,000 satellites × 40 path steps = ~240,000 propagations per path update cycle (~2s computation)
- Dots update every 3s, paths every 30s
- Longitude-wrap detection breaks line segments at the antimeridian to prevent rendering artifacts

### Altitude Scaling

Satellite altitudes are compressed for visual clarity:
- LEO (400 km): rendered at ~0.08 globe radii above surface
- MEO/GPS (20,200 km): compressed to ~0.5 globe radii
- Formula: `alt < 0.15 ? alt + 0.02 : 0.15 + sqrt(alt - 0.15) * 0.3`

## Development

Open `index.html` in any browser. No build step required.

```sh
# Clone
git clone https://github.com/sardoru/adham-rocket-launches.git
cd adham-rocket-launches

# Open locally
open index.html

# Deploy to Vercel
npx vercel --prod
```

## Deployment

Hosted on [Vercel](https://vercel.com) with auto-deploy on push to `main`.

- GitHub: [github.com/sardoru/adham-rocket-launches](https://github.com/sardoru/adham-rocket-launches)
- Live: [adham-rocket-launches.vercel.app](https://adham-rocket-launches.vercel.app)

## License

MIT
