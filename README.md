# GRC Executive Dashboard — NIST CSF 2.0

A single-file, interactive executive dashboard for tracking cybersecurity governance, risk, and compliance against the **NIST Cybersecurity Framework 2.0**. Built for C-level / board reporting, with built-in step-by-step validation so progress claims are backed by evidence, not just status updates.

[Live preview](https://M-Sugiarto.github.io/grc-nist-dashboard/) — open `index.html` directly in any browser, no build step, no server required.

## What it does

- **Function map** — NIST's six CSF 2.0 functions (Govern, Identify, Protect, Detect, Respond, Recover) visualized as NIST's own wheel, with Govern at the center.
- **Maturity scoring** — every one of the 23 CSF categories is scored 0–100% based on a 4-stage validation gate: **Documented → Implemented → Tested → Validated**. Nothing counts as "done" until it's been through all four.
- **Executive KPIs** — overall maturity %, NIST maturity tier (1–4), open items, functions on track, and a prioritized "needs board attention" list.
- **Roadmap** — a phased implementation plan with auto-calculated progress (no manual entry).
- **Audit log** — every status change is timestamped automatically for board and auditor review.
- **Export / Import** — save your data as JSON to reload later, or export to CSV for spreadsheets and board packs. Everything runs client-side; no data leaves the browser.

## Usage

1. Download or clone this repo.
2. Open `index.html` in any modern browser.
3. Click through the tabs: Executive Overview, Framework Map, Roadmap, Audit Log.
4. Update statuses, owners, and target dates to reflect your organization.
5. Use **Export data** to save your progress, **Import** to reload it next session.

No installation, dependencies, or backend required — it's a single static HTML file.

## Customizing

All framework data lives in the `FUNCTIONS` array near the top of the `<script>` block in `index.html`. Each category has:

```js
{code:'GV.OC', name:'Organizational Context', owner:'Board / CEO', status:4, target:'Q1 2026', note:'...'}
```

Edit owners, targets, and starting statuses to match your environment, or extend the categories to match a different framework entirely.

## Disclaimer

This tool is a tracking and reporting aid, not a certified compliance assessment. It does not constitute legal or audit advice, and alignment with NIST CSF 2.0 should be independently verified as part of any formal compliance program.

## License

MIT — see [LICENSE](LICENSE). Fork it, modify it, use it commercially, no permission needed.
