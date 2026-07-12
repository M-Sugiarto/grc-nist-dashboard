# GRC Executive Dashboard — NIST CSF 2.0 & AI RMF 1.0

A single-file, interactive executive dashboard for tracking governance, risk, and compliance against **two** NIST frameworks — the **Cybersecurity Framework 2.0** and the **AI Risk Management Framework 1.0 (AI RMF)** — in one place. Built for C-level / board reporting, with built-in step-by-step validation so progress claims are backed by evidence, not just status updates.

[Live preview](https://M-Sugiarto.github.io/grc-nist-dashboard/) — open `index.html` directly in any browser, no build step, no server required.

## What's new in this fork

The original dashboard tracked NIST CSF 2.0 only. This version adds a **framework switcher** in the top bar so the same dashboard can track cybersecurity risk (CSF 2.0) *and* AI risk (AI RMF 1.0) side by side:

- Each framework keeps its **own** function map, category scores, roadmap, and audit log — switching frameworks never overwrites the other's data.
- The **organization profile** (name, scope, industry, prepared by) is shared across both, since it's the same organization being assessed.
- **Export data** now saves both frameworks' full state (plus the org profile) into a single JSON file; **Import** restores both. Legacy single-framework export files from the original CSF-only dashboard still import cleanly into the CSF side.
- **Export CSV** exports whichever framework is currently active, tagged with a `Framework` column.

## What it does

- **Function map** — each framework's functions are visualized as NIST's own wheel, with **Govern** at the center in both:
  - NIST CSF 2.0: Govern, Identify, Protect, Detect, Respond, Recover (6 functions, 23 categories).
  - NIST AI RMF 1.0: Govern, Map, Measure, Manage (4 functions, 19 categories).
- **Maturity scoring** — every category in either framework is scored 0–100% based on the same 4-stage validation gate: **Documented → Implemented → Tested → Validated**. Nothing counts as "done" until it's been through all four.
- **Executive KPIs** — overall maturity %, a maturity band, open items, functions on track, and a prioritized "needs board attention" list — all scoped to the active framework.
- **Roadmap** — a phased implementation plan per framework with auto-calculated progress (no manual entry).
- **Audit log** — every status change is timestamped automatically for board and auditor review, kept separately per framework.
- **Export / Import** — save your data as JSON to reload later (both frameworks in one file), or export the active framework to CSV for spreadsheets and board packs. Everything runs client-side; no data leaves the browser.

### A note on maturity language

NIST CSF 2.0 defines its own four **Implementation Tiers** (Partial, Risk Informed, Repeatable, Adaptive), so the CSF side of the dashboard uses NIST's own tier names.

NIST AI RMF 1.0 does **not** define an equivalent maturity model — it's deliberately left to each organization. So the AI RMF side instead shows a **"Program maturity band"** (Ad hoc → Developing → Managed → Optimizing). This is this dashboard's own internal scale for tracking progress, not an official NIST construct, and the dashboard says so in the UI.

## Usage

1. Download or clone this repo.
2. Open `index.html` in any modern browser.
3. Pick a framework in the **Framework** bar: **NIST CSF 2.0** or **NIST AI RMF 1.0**.
4. Click through the tabs: Executive Overview, Framework Map, Roadmap, Audit Log.
5. Update statuses, owners, and target dates to reflect your organization.
6. Use **Export data** to save your progress (both frameworks), **Import** to reload it next session.

No installation, dependencies, or backend required — it's a single static HTML file.

## Customizing

All framework data lives in two builder functions near the top of the `<script>` block in `index.html`:

- `buildCsfStore()` — the NIST CSF 2.0 function/category/module data.
- `buildAirmfStore()` — the NIST AI RMF 1.0 function/category/module data.

Each category holds one or more modules shaped like:

```js
M('Organization-wide', 'Board / CAIO', 3, 'Q1 2026', '', 'AI policy suite and inventory process implemented; annual review cadence being tested.')
// label, owner, status (0-4), target, ticket, note
```

Edit owners, targets, and starting statuses to match your environment, or extend the categories to match a different framework entirely (a third framework just needs its own `build*Store()` function, an entry in `FRAMEWORK_META`, and a button in the `frameworkbar`).

Framework-level display text (titles, subtitles, tier definitions) lives in the `FRAMEWORK_META` object, so wording can be tailored per framework without touching the rendering logic.

## Disclaimer

This tool is a tracking and reporting aid, not a certified compliance assessment. It does not constitute legal or audit advice. Alignment with NIST CSF 2.0 and/or NIST AI RMF 1.0 should be independently verified as part of any formal compliance or assurance program. The AI RMF "program maturity band" shown in this dashboard is not an official NIST maturity model.

## License

MIT — see [LICENSE](LICENSE). Fork it, modify it, use it commercially, no permission needed.
