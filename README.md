# HRV Pacer

A minimal, single-file breathing pacer for the web. Pick a protocol, follow the
expanding/contracting circle, and pace your breath for activation, recovery,
stress relief, or sleep. No build step, no dependencies, no tracking — it's one
`index.html`.

## Quick start with Claude

Want it running for yourself without touching a terminal? Paste the prompt below
into your own Claude account and let it walk you through forking, deploying, and
wiring up the heart-rate debrief.

```text
I want to set up the HRV Pacer breathing app for myself. The repo is a single
static index.html (no build step, no dependencies). Walk me through it one step
at a time, waiting for me to confirm each step before moving on:

1. Fork or copy the repo (https://github.com/chivnerd/hrv-pacer) to my own GitHub.
2. Enable GitHub Pages so it's live at my-username.github.io/hrv-pacer — tell me
   exactly which settings to click.
3. Help me add the live page to my iPhone home screen as a full-screen web app.
4. Explain how to capture heart rate per session: starting an "Other" workout on
   my Apple Watch around each breathing session, and what read-only HealthKit
   (Heart Rate) access you need so you can analyze a session afterward. Tell me
   what to enable on my end.
5. (Optional) Show me where in index.html the PROTOCOLS object is so I can tweak
   breath timings or add my own protocol.

Assume I'm not a developer. Keep each step short and concrete.
```

## Protocols

| Mode | Pattern | Default | Purpose |
|------|---------|---------|---------|
| ⚡ **Pre-Workout** | Box 4·4·4·4 | 15 rounds (~4 min) | Activation |
| 🔄 **Post-Workout** | Resonance 5.5·5.5 | 10 rounds (~2 min) | HRV recovery |
| 🧘 **Cortisol Reset** | Physiological sigh (2·1·8) | 10 rounds (~2 min) | Stress relief |
| 🌙 **Pre-Sleep** | 4-7-8 | 8 rounds (~2.5 min) | Wind-down |

Each mode offers a few duration presets, and your last-used choice is remembered
per mode via `localStorage`.

## Features

- **Animated breath circle** with a progress ring that tracks each phase.
- **Pause / resume** with accurate, drift-free timing — the countdown is driven
  by absolute timestamps, so backgrounded or throttled tabs stay in sync with
  real elapsed time.
- **Session debrief** — after a session, the **DEBRIEF** button copies a ready-made
  prompt (protocol, rounds, start/end times) to your clipboard. Paste it into
  Claude to pull your heart-rate samples from HealthKit for that window and see
  how your HR trended across the session.
- **Mobile-first** — designed for phones, works as an iOS home-screen web app
  (`apple-mobile-web-app-capable`), with touch-friendly tap handling.

## Capturing heart rate (Apple Watch + Claude)

The pacer works standalone, but the **debrief** flow is built to pair it with an
Apple Watch recording and Claude's read access to HealthKit. The Watch only logs
continuous, high-frequency heart-rate samples while a *workout* is active, so you
start a workout for the duration of the breathing session, then ask Claude to
analyze that exact window afterward.

### 1. Start the workout on your Apple Watch

1. Open the **Workout** app on the Watch.
2. Choose **Other** (scroll to the bottom of the workout list). "Other" is used
   deliberately — it records heart rate without assuming a specific activity, so
   it won't skew your activity rings or training metrics.
3. Start it **just before** you tap START in the pacer, and **end + save** it
   right after the session finishes. This keeps the HR samples tightly scoped to
   the breathing window.

> Why a workout at all? Outside a workout, the Watch samples HR only
> intermittently (every few minutes). During a workout it records near-continuous
> samples — which is what makes a per-session HR trend meaningful.

### 2. Run the session

Pick a protocol in the pacer, choose a duration, and breathe along. When it
completes, tap **DEBRIEF** — this copies a prompt containing the protocol, round
count, and the session's start/end times.

### 3. Let Claude read HealthKit and analyze

Paste the copied prompt into Claude. For Claude to actually pull the samples, it
needs **read access to your Health data — specifically Heart Rate** — through
whatever HealthKit bridge you use (for example, a HealthKit-enabled MCP server,
or an export/share of the relevant Health data). Grant **read-only** access;
nothing here writes to Health.

The prompt scopes the request to the session's time window, so Claude can fetch
just the heart-rate samples for that span and show how your HR trended across the
breathing session.

#### Prompt to share with your Claude

The **DEBRIEF** button copies a session-specific prompt automatically, but if you
want to paste it manually (or prime Claude on what you're after), use this — fill
in the bracketed values from the completed session:

```text
I just finished an HRV breathing session and want to see how it affected my heart rate.

Session details:
- Protocol: [e.g. BOX 4·4·4·4]
- Rounds: [e.g. 15]
- Started: ~[start time]
- Ended: ~[end time]

Using my HealthKit data (read-only), pull my heart-rate samples for that exact
time window and analyze the session:
1. Show how HR trended from start to end (a simple sparkline or min/avg/max is fine).
2. Report my starting HR, ending HR, and the overall change.
3. Tell me whether HR settled/declined over the session, which is what I'd expect
   from effective paced breathing.

If you don't yet have access to my Health data, tell me what to enable so you can
read Heart Rate for that window. Don't write anything to HealthKit — read only.
```

> Tip: you can save this as a Claude Project instruction or a saved prompt so each
> session debrief is one paste away.

> **Permissions summary:** Apple Watch → an active **Other** workout for the
> session. Claude → **read** access to HealthKit **Heart Rate** for the session's
> time window. No write permissions are needed, and the pacer itself stores
> nothing beyond your last-used duration in `localStorage`.

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
