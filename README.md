# Sauron — the product

The consumer-facing product: a lightweight **static site** that displays the
Edge model's current 10-stock book and its 2-year backtest. It holds no model
code — it just renders a data snapshot exported from the in-house Edge repo.

```
the Edge repo (in-house model + viewer)        sauron (this folder, the product)
  edge_lib / edge_tracker_lib  ──►  export_sauron.py  ──►  sauron_data.js  ──►  index.html
```

## Files
- `index.html` — the product UI (3 screens: capital → 10 positions → dashboard).
  Reads `window.SAURON_DATA`; if the feed is missing it falls back to "N/A".
- `sauron_data.js` — **generated** data feed. `window.SAURON_DATA = {…}` with the
  current 10-stock book, the 2-year backtest curve, and headline stats. Do not
  edit by hand.

## Product configuration (baked into the export)
- 2-year window · 1-month rebalance (hold=21) · 75% YoY-revenue-growth mix ·
  10-stock equal-weight book.

## Refreshing the data (the pipeline)
From the in-house Edge repo (`../quant model`):

```
.venv/Scripts/python.exe export_sauron.py
```

That recomputes the model and rewrites `sauron_data.js` here. Re-run it whenever
you want to push fresh picks (e.g. after a new monthly rebalance).

## Viewing locally
Because the feed is plain JS (not fetched JSON), you can just open `index.html`
directly, or serve the folder:

```
python -m http.server 5055    # then open http://127.0.0.1:5055
```

## Hosting
This folder is fully static (HTML + one generated JS file + Google Fonts), so it
can go on any static host — Netlify, Vercel, GitHub Pages, Cloudflare Pages — for
free. Deploy the folder; refresh `sauron_data.js` via the pipeline and redeploy
to update the picks.

## Notes
- No API key or secret is needed: the export contains only the public picks +
  backtest curve, nothing sensitive. (If you later want the site to pull *live*
  from a deployed Edge server instead of a snapshot, that would need the server
  hosted + an endpoint — but the snapshot pipeline is simpler and is the default.)
- Performance shown is a historical backtest, net of estimated costs — not a
  forecast. Same honesty caveats as the in-house model (survivorship, momentum
  regime, etc.).
