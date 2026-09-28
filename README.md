# HRV Pacer

A minimal, single-file breathing pacer for the web. Pick a protocol, follow the
expanding/contracting circle, and pace your breath for activation, recovery,
stress relief, or sleep. No build step, no dependencies, no tracking — it's one
`index.html`.

## Protocols

| Mode | Pattern | Default | Purpose |
|------|---------|---------|---------|
| Pre-Workout | Box 4·4·4·4 | 15 rounds (~4 min) | Activation |
| Post-Workout | Slow breathing, 6s in / 6s out (5 breaths/min) | 10 rounds (~2 min) | Recovery |
| Find Your Rate | 6.5, 6, 5.5, 5, 4.5 breaths/min; 2 min each, no holds | 10 min | Compare paced rates |
| Cortisol Reset | Physiological sigh (2·1·8) | 10 rounds (~2 min) | Stress relief |
| Pre-Sleep | 4-7-8 | 8 rounds (~2.5 min) | Wind-down |

The rate test uses a 45% inhale and 55% exhale. It is a pacing test, not a measurement:
this web page does not collect heart-rate data or choose an optimal rate. Wear your
Apple Watch, start an "Other" workout before the test, and save it after. Compare
clean heart-rate waves across the five dated two-minute stages later; watch HR
sampling may be irregular or smoothed. Do not assume a rising or falling average
heart rate proves resonance. Pause if uncomfortable.

## Features

- Animated breath circle and phase progress ring, timed from active elapsed time.
  Backgrounded tabs skip missed cues and finish when the planned active time elapses.
- Pause/resume preserves the current phase and records pause spans.
- **DEBRIEF** copies a destination-neutral record: full ISO timestamps, local
  time and timezone, phases, rounds, wall time, active time and pause spans.
  It contains no sensor reading; sharing it or giving a service Health access
  is a separate, explicit choice. The pacer never uploads health information.
- Last-used round choice for each ordinary mode is saved locally.
- Mobile-first static page; no build or app dependencies.

## Usage

It's a static page — no install required.

**Run locally:**
```bash
# clone, then just open the file
open index.html          # macOS
# or serve it for full clipboard/PWA behavior
python3 -m http.server 8000   # then visit http://localhost:8000
```

**Host it free on GitHub Pages:**
Settings → Pages → Build and deployment → Deploy from a branch → `main` / root.
Your pacer will be live at `https://<username>.github.io/hrv-pacer/`.

**Add to your phone:** open the hosted URL in Safari → Share → *Add to Home
Screen* for a full-screen, app-like experience.

## How it works

Everything lives in `index.html`:

- **`PROTOCOLS`** — a data-driven definition of each mode (name, badge, round
  options, and an array of breath phases with label/duration/animation style).
  Adding a new protocol is just another entry in this object.
- **`tick()`** — the session loop. It computes remaining phase/session time from
  `phaseEndTime` / `totalEndTime` timestamps rather than decrementing a counter,
  which keeps timing accurate across pauses and tab throttling.
- **CSS animations** drive the breath circle (`expand`, `contract`, sigh
  variants); the SVG ring shows phase progress.

## License

No license file is included yet. If you'd like others to reuse it, consider
adding one (MIT is a common choice for small projects like this).
