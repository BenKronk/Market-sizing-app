# SOM Intensity Sizer

A market-sizing tool that folds competitive intensity into the SOM (Serviceable Obtainable Market) estimate, instead of leaving it as an arbitrary slider.

Most TAM/SAM/SOM calculators let you type any number for SOM. This one decomposes that number into factors you can each defend — a concentration-anchored fair share, a contestability haircut, and a near-term ramp — and checks the result against the 1–5% credibility band investors expect.

> **Status:** working prototype. The model is a defensible construction, not a validated methodology. See [Limitations](#limitations) before relying on the output.

---

## What it does

- Computes market concentration (**HHI**, **CR4**, effective number of firms) from competitor market shares you enter.
- Anchors a fair-share estimate to concentration, then discounts it for contestability and near-term ramp.
- Returns an estimated SOM in dollars and as a percentage of SAM.
- Flags whether the result lands inside, below, or above the 1–5%-of-SAM sanity band.

## The model

```
SOM = SAM × S_fair × Contestability × Ramp
```

| Factor | Meaning | How it's set |
| --- | --- | --- |
| **SAM** | Serviceable available market ($) | User input |
| **S_fair** | Fair-share anchor — an *average* entrant's long-run share | Equals fractional HHI (computed from competitor shares) |
| **Contestability** | Discount for what HHI can't see | Combines switching costs, entry barriers, and your differentiation |
| **Ramp** | Fraction of long-run share reachable in years 1–3 | User input |

**Why S_fair ≈ HHI:** with *N* equal-share firms, each holds `1/N` and HHI (fractional) equals `1/N`. So an average entrant's steady-state share approximates the HHI. This *centers* the estimate — it does not predict a differentiated or weak entrant's outcome.

**Why the 1–5% band:** early-stage capture of 1–5% of the serviceable market over years 1–3 is a common credibility check, and claiming much more than ~5% early tends to read as aggressive. The band is a heuristic sanity check, not a rule.

## Running it locally

The tool is a single, dependency-free React component (`som-intensity-sizer.jsx`) — it imports only React. To run it standalone, drop it into any React app. A minimal path with Vite:

```bash
npm create vite@latest som-sizer -- --template react
cd som-sizer
# replace src/App.jsx with som-intensity-sizer.jsx (and update the import in main.jsx)
npm install
npm run dev
```

Or paste the component into an existing React project and render `<SomIntensitySizer />`.

## Usage

1. Enter your **SAM** (annual, in dollars).
2. Add each known competitor's **market share (%)**. These drive HHI/CR4.
3. Set the three **contestability** sliders — switching costs, barriers to entry, your differentiation.
4. Set the **ramp** for what fraction of long-run share is winnable in the near term.
5. Read the SOM output and check the band flag. If the result falls well outside 1–5%, revisit the inputs rather than trusting the number.

## Limitations

Read these before using the output for anything real:

- **Coefficients are judgment calls.** The contestability weighting (0.45 / 0.30 / 0.25 across the three sliders) is a construction, not an empirically validated formula. Calibrate it against real outcomes before trusting the exact figure.
- **The fair-share anchor assumes an average entrant.** It centers the estimate; it does not model a strong or weak competitive position.
- **Point estimate, not a range.** The tool returns one number. Real uncertainty in the inputs is not yet represented (see roadmap).
- **Competitor shares are only as good as your data.** HHI computed from public players alone is biased toward whatever the public firms dominate and misses the private/fragmented tail.

The value of the tool is that every assumption is made explicit and arguable — not that any single output is correct.

## Roadmap

Candidate features, roughly in priority order:

- **Conjoint-measured attractiveness** — accept share-of-preference outputs from a conjoint study and convert them to the relative-attractiveness input `r`, replacing the judgment estimate with measured data. This closes the tool's one irreducible soft spot: `r` is the only input no model can supply, and a conjoint's logit share-of-preference is exactly a Bell-Keeney-Little attraction estimate, so it drops straight in. *Requires a conjoint study — not yet available, so the input mode is deferred until real outputs exist.*
- **Monte Carlo range** — treat coefficients as distributions and output a SOM confidence interval instead of a point estimate.
- **Win-rate calibration** — back the contestability coefficient out of historical win rates against named competitors, rather than guessing the sliders.
- **Bottom-up cross-check** — add a `customers × ACV × reachable geography` path and flag whether top-down and bottom-up converge.
- **Time-phased ramp** — replace the single ramp slider with a multi-year adoption curve, yielding a revenue projection.
- **Sensitivity / tornado view** — rank which input moves SOM most.
- **Data ingestion** — pull competitor shares from a source (e.g. SEC EDGAR + Census for a derived estimate, or a licensed market-data API) instead of manual entry.

## License

MIT — see [LICENSE](LICENSE). Change this if you'd prefer something more restrictive.
