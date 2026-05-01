# Saturation Studio

Upload a CSV of daily ad spend + revenue. Get a Hill saturation curve, optimal-spend recommendations across LTV horizons, and a scenario-modeling chart.

**Runs entirely in your browser.** Your CSV never leaves your machine — no server, no upload, no telemetry.

## Use it

**Easiest:** [open the live version](https://YOUR-USERNAME.github.io/saturation-tool/) (replace `YOUR-USERNAME` with your GitHub handle once you've enabled GitHub Pages — see "Host" below).

**Offline:** download `index.html` and open it in any browser. Single file, no install.

## CSV format

Download [`template.csv`](./template.csv) for a starter file.

| Column | Required | Notes |
|---|---|---|
| `date` | yes | `YYYY-MM-DD` or `M/D/YYYY` |
| `spend` | yes | Daily ad spend in dollars (raw numbers — `12450`, not `$12,450`) |
| `revenue` | yes | Daily attributed ad revenue |
| `purchases` | optional | Daily order count — used to project orders / new customers |

90+ days recommended. 365 days gives the most stable fit. Days with $0 spend or $0 revenue are excluded automatically.

## What it computes

- **Hill saturation fit** on your daily data: `revenue = V_max · s^h / (K^h + s^h)`
- **Optimal daily spend** for seven different goals:
  - Max contribution margin today
  - Max 3-month / 6-month / 12-month LTV-extended CM
  - Max revenue, max NC revenue
  - Target ROAS (with peak-ROAS bisection — flags unreachable targets)
- **Per-month curves** when a month has 8+ observations; otherwise the global fit scaled by month-of-year seasonality
- **Recency-weighted fit** (default 60-day half-life) so the curve reflects the current efficiency regime, not last year's

## Knobs you control

| | Default | What it does |
|---|---|---|
| Variable cost % | 35 | COGS + shipping + processing as % of revenue |
| NC share % | 60 | Share of revenue from new customers |
| Median AOV | from data | Used to project orders and new customers |
| 3m / 6m / 12m LTV | 1.4× / 1.8× / 2.5× | Cumulative customer revenue at horizon ÷ first-order revenue. **The single most important inputs — pull yours from your cohort tool.** |
| Recency half-life | 60 days | A point N days ago gets weight `0.5^(N/half_life)` in the fit |
| Adstock decay (λ) | 0 | Steady-state spend gain = `1/(1-λ)`. Try 0.3 for typical Meta carryover. |
| Efficiency adjust | 0% | Scale `V_max` if you expect the platform to deliver better/worse than the trailing fit |
| Target ROAS | 1.2× | Solves backwards: max spend that still hits this ROAS |
| Today's daily spend | from data avg | Where the "today" line sits on the chart |

## Host on GitHub Pages

```bash
# 1. Push this repo to GitHub
git remote add origin https://github.com/YOUR-USERNAME/saturation-tool.git
git push -u origin main

# 2. On the GitHub repo page:
#    Settings → Pages → Source → Deploy from branch
#    Branch: main, folder: / (root) → Save

# 3. Wait ~30s, your tool is live at:
#    https://YOUR-USERNAME.github.io/saturation-tool/
```

That URL is what you share with anyone — no install, just upload their CSV.

## Caveats

The model is **directional**, not precise:

- **LTV multipliers are doing most of the work.** Generic DTC defaults probably aren't right for your cohort. A 12-month LTV swing of 2.5× → 1.5× flips most recommendations.
- **Recommendations beyond your observed spend range are extrapolation.** If your data tops out at $30K/day, a recommendation of $60K/day is a model guess.
- **The fit is observational, not causal.** Reported revenue could be over-attributed by the platform. A geo lift study (GeoLift, etc.) tells you the genuine incremental ROAS — feed that into your VC% / LTV inputs.
- **Cash flow timing matters.** Math says profitable at month 12 — but you spent on day 1 and won't see the repeat revenue for months. Use a discounted cash flow if working capital is tight.

## License

MIT — do whatever you want with it.
