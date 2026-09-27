# Seed Sorting Machine — Operator Dashboard

Streamlit monitoring & control interface for the automated maize seed
sorting system (Chapter 3, sections 3.3–3.4).

## Files
- `app.py` — the dashboard UI (Live Dashboard tab + History tab)
- `serial_bridge.py` — USB serial bridge (Live mode) + Simulation bridge
- `cloud_bridge.py` — wireless bridge over Firebase (Cloud mode)
- `requirements.txt` — dependencies

## Setup
```bash
pip install -r requirements.txt
streamlit run app.py
```

## Three connection modes (sidebar)
1. **Simulation** — no hardware, generates fake data.
2. **Live (USB)** — dashboard and Pi wired together by USB, 9600 baud serial.
3. **Cloud (WiFi)** — Pi and dashboard talk over the internet through a
   free Firebase Realtime Database — works from a phone anywhere.

## How sessions & history work
Every time you press **START**, a new "session" begins recording. When
you press **STOP**, that run is saved into the **History** tab with its
own good/bad/total counts, a trend graph, and a downloadable CSV — so
you (or your supervisor) can go back and review any previous run, not
just what's happening live. The Live Dashboard tab always shows the
*current* run in progress.

## The labelling protocol (from the vision/IoT side)
The vision model outputs a simple binary label per seed:
- `0` = bad / defective seed
- `1` = good seed

**Over USB (Live mode):** the Pi can just send `0` or `1` on its own
line per detection (or `GOOD`/`BAD`, or `COUNTS:<g>,<b>` for cumulative
totals) — `serial_bridge.py` understands all three formats.

**Over Cloud (Firebase mode):** since Firebase is read as a snapshot,
the Pi should keep a running tally of the 0/1 labels itself and PATCH
the current `good`/`bad` totals every second or so — full example code
is in the docstring at the top of `cloud_bridge.py`.

## Hosting the dashboard so it's reachable from a phone anywhere
1. Push this folder to a GitHub repo.
2. Go to **share.streamlit.io**, sign in with GitHub.
3. New app → pick the repo → main file `app.py` → Deploy.
4. You get a public URL — open it from any phone/browser, select Cloud
   mode, paste your Firebase URL, and it's live.

## Dashboard features
- Machine Status, Good Seeds, Bad Seeds, Total Maize Counted
- Good-seed rate progress bar
- Live camera feed (labelled good/bad frame from the vision model)
- Clean Altair graphs — a good/bad/total trend line plus a good-vs-bad
  bar chart, both with a proper legend and colors (green=good, red=bad)
- **History tab** — every completed run saved with its own stats, graph,
  and CSV download; plus a combined CSV of all sessions
- Modern dark theme with responsive layout (stacks cleanly on phones)
- START / STOP controls, identical across all three connection modes
