# California Bridge Time-Series Engine

The shared data foundation for the California ABC platform. It turns 7 years of
FHWA National Bridge Inventory submittals (2019–2025) into one browsable,
filterable, mapped model of every California bridge — with a **deterioration
trajectory** computed per structure, not just a static snapshot.

## What it does

- **25,975 California bridges**, each carrying its full 2019→2025 condition
  history for deck, superstructure, substructure, channel, and culvert.
- **Deterioration analysis per bridge**: 7-year linear rate, number of rating
  drops vs. recoveries, net change, and a projected year-to-Poor at the current
  rate.
- **Three views**:
  - **Table** — sortable by fastest-declining, worst condition, highest traffic,
    soonest-to-Poor, most drops, longest span, oldest built. Inline sparklines
    show each bridge's 3-component trajectory at a glance.
  - **Map** — every bridge plotted, colored by condition, ringed if in sustained
    decline, sized by traffic.
  - **Overview** — portfolio dashboard: condition mix, where risk concentrates by
    county, inventory by decade built, and the headline counts.
- **Detail panel** — full 7-year multi-component curve, deterioration verdict in
  plain language, structure/age/traffic/rating/scour, and a Google Maps link.

## Filters

Condition (Good/Fair/Poor), sustained-decline-only, projected-to-Poor-by-2035,
recent-rehab-detected, min ADT, scour-critical, load-posted, min span length,
built-before-year, never-reconstructed, and free-text search across name, route,
ID, and county.

## Key definitions

- **Condition (GFP)** follows FHWA: lowest of deck/super/sub/culvert — ≥7 Good,
  5–6 Fair, ≤4 Poor.
- **Sustained decline** = 7-year rate < −0.1/yr, at least 2 rating drops, and no
  recoveries. This deliberately strict filter separates real physical
  deterioration from single-year inspector re-ratings.
- **Recent rehab** = an upward jump of ≥2 rating points in the series (a work
  event proxy, since legacy NBI has no work-history item).
- **Closed bridges** (Item 41 = K) are excluded from deterioration math so a
  closure doesn't read as a 9→0 collapse.

## Honest caveats

- **No seismic field exists in legacy NBI.** Seismic risk must be *derived* (age +
  reconstruction status + fault proximity) or computed live (USGS) in the
  triage tool — it is not a reported column here.
- **Short histories** (bridges with <3 real data points, e.g. new or renumbered
  structures) get no deterioration rate rather than a misleading one.
- **Projections are linear extrapolations**, useful for screening and
  prioritization, not a substitute for engineering assessment.
- Ratings are integers, so small real changes may not register year to year.

## Files

- `index.html` — the app (single file, no build step).
- `ca_bridges.json` — preprocessed California data (~12 MB, ~1.4 MB gzipped).

## Deploy

Drop both files in a GitHub repo, enable Pages. That's it — no server, no build.
The app fetches `ca_bridges.json` from the same directory. Locally, serve the
folder (`python3 -m http.server`) rather than opening the file directly, so the
fetch works.

## Regenerating the data

The JSON is built from the 7 FHWA "AllStates NoDelimiter" fixed-width files by a
Python preprocessor that parses per the 1995 Recording & Coding Guide field
positions, extracts California (state code 06), converts DMS coordinates to
decimal degrees and metric tenths to meters, and computes the deterioration
fields. To add more years or another state, extend the preprocessor and rebuild.

## Where this sits in the platform

This is deliverable #1 — the foundation the other four features read from:
Risk Heat Map, Twins/clustering, ABC recommender, Constructability, and
Post-event seismic triage (USGS). Each layers on top of this same bridge model.
