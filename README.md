# Ganaraska River — Live Model Outputs (Experimental)

**Live site → https://tomgrizz.github.io/ganaraska-live/**

A computer-vision model watches the Ganaraska River fishway counter tunnel camera in
real time, identifies each salmon or trout as it swims through, tracks which way it is
heading, and tallies the run. The page shows the current count, the latest clip the
model identified, this year's run against past years, and every fish it has flagged —
plus a short explainer of how the model works.

> ⚠️ **Experimental.** This runs alongside the counter's official Vaki/Maricam system as
> a parallel, secondary read — it does **not** interfere with or replace the official
> count. The numbers here are model outputs under evaluation, not verified counts.

## What the model does

- **Detects** each fish in the tunnel with a DETR detector, **crops** it, and
  **identifies** the species with a DINOv3 image classifier — Chinook, Coho, Atlantic
  salmon, Rainbow (steelhead), and Brown trout.
- **Tracks** each fish across the whole passage and reads its **overall direction**
  (heading upstream vs. downstream) to count net upstream migration.
- Clips are cut automatically when a fish is present and processed as they arrive; the
  page refreshes on its own as new fish are identified.

## About this repo

This repository hosts the live web page via GitHub Pages. `index.html` is the page
itself; the `*.json` files, `latest_model_id.mp4`, and the chart images are refreshed
automatically by the model pipeline. Edits made to `index.html` here are preserved
across those automatic updates.

## Data files the page reads

| File | Written by | What it is |
|---|---|---|
| `model_live.json` | counting machine, every few min | season totals, per-day counts (`daily`), latest clip name |
| `events.json`, `run_series.json` | counting machine | every counted passage (newest first); the 2026 cumulative Chinook line |
| `status.json` | counting machine, heartbeat every ~10 min | pipeline health for the status light: feed frames arriving?, cutter lag, last frame time |
| `riverwatcher.json` | counting machine, hourly | the counter's own raw daily up/down from riverwatcherdaily.is (all species, not validated), shown alongside the model |
| `atl_candidates.json`, `atl_confirmed.json`, `atl_cand/` | counting machine | Atlantic-salmon review list and the reviewer-confirmed log |

Status light: **green** = frames arriving and the cutter is caught up; **amber** = catching up after a stall;
**red** = no frames from the counter (feed offline since the time shown) or no heartbeat at all (the counting
machine or its publisher is down). Clips tagged *recovered* were pulled from the counter PC's own iSpy
recordings after a link outage and counted after the fact.
