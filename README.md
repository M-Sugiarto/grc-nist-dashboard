# GRC Executive Dashboard — NIST CSF 2.0 & AI RMF 1.0

A single-file, interactive executive dashboard for tracking governance, risk, and compliance against **two** NIST frameworks — the **Cybersecurity Framework 2.0** and the **AI Risk Management Framework 1.0 (AI RMF)** — in one place. Built for C-level / board reporting, with built-in step-by-step validation so progress claims are backed by evidence, not just status updates.

[Live preview](https://M-Sugiarto.github.io/grc-nist-dashboard/) — open `index.html` directly in any browser, no build step, no server required.

## What's new in this fork

The original dashboard tracked NIST CSF 2.0 only. This version adds a **framework switcher** in the top bar so the same dashboard can track cybersecurity risk (CSF 2.0) *and* AI risk (AI RMF 1.0) side by side:

- Each framework keeps its **own** function map, category scores, roadmap, and audit log — switching frameworks never overwrites the other's data.
- The **organization profile** (name, scope, industry, prepared by) is shared across both, since it's the same organization being assessed.
- **Export data** now saves both frameworks' full state (plus the org profile) into a single JSON file; **Import** restores both. Legacy single-framework export files from the original CSF-only dashboard still import cleanly into the CSF side.
- **Export CSV** exports whichever framework is currently active, tagged with a `Framework` column.
- **Security hardening**: audit log entries and the module Target field are now HTML-escaped before rendering, closing a stored/self-XSS gap that a crafted Target value or an imported JSON file could otherwise trigger. See [Known security limitations](#known-security-limitations) below — this hardening is partial, not complete.

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

## Security

This is a client-side-only tool — there's no server, no network calls, no analytics. That said, "runs locally" does not mean "no risk," and anyone using this dashboard should know what's been fixed and what hasn't.

### Fixed: audit log & Target field XSS

Earlier versions of this dashboard rendered several fields into the page without HTML-escaping them: the audit log's timestamp, function name, category code, and from/to status text, and the module **Target** field in the "Needs board attention" list. Because the Target field is directly editable in the UI, and because the audit log (like the rest of the dashboard's data) can be replaced wholesale via **Import**, either path could be used to inject and execute arbitrary HTML/JavaScript in your browser tab — for example, typing `<img src=x onerror=alert(1)>` into Target, or importing a JSON file with a crafted `func` or `ts` value in an audit log entry.

This has been fixed: all of those fields are now passed through an `escapeHtml()` call before being inserted into the page, so injected markup renders as inert text instead of executing. This was verified by injecting `<script>`, `<svg onload=...>`, and `<img onerror=...>` payloads into both paths and confirming they render escaped rather than execute.

### Known security limitations (not yet fixed)

The **Import** feature accepts an arbitrary JSON file and loads it directly into the dashboard's data model, no schema or content validation. Beyond the audit log and Target field above, several other fields sourced from that data — category `name` and `code`, and function `name` and `desc` — are still rendered **without** escaping in a few places: the Framework Map category list, the function selector chips, the wheel's SVG tooltips, and the roadmap's phase tags. A maliciously crafted JSON file could use these fields to inject and run script in your browser when imported.

Because this tool has no backend and no network calls of its own, the practical impact of that script execution is limited to whatever the page itself can do in your browser (e.g. manipulating what's displayed, or using any browser APIs available to the tab) — but it should still be treated as a real risk, not a theoretical one. Note that opening a malicious `index.html` directly, rather than a malicious *data* file inside a trusted `index.html`, is a different and larger risk than what's described here — only run a copy of `index.html` that you or someone you trust reviewed.

**Until this is fully fixed, treat Import like you would any other file from an untrusted source:**
- Only import JSON files you exported yourself from this same dashboard, or that came from someone you trust.
- Don't import `.json` files received unsolicited (e.g. via email, a shared drive, or a support ticket) without inspecting them first — they're plain text, so opening one in a text editor to sanity-check it before importing is a reasonable precaution.
- If you fork this project and hardening the remaining fields matters for your use case (e.g. you plan to accept import files from people outside your organization), extend the same `escapeHtml()` treatment to every field rendered from `FUNCTIONS`/`PHASES` data before use.

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

This tool is a tracking and reporting aid, not a certified compliance assessment. It does not constitute legal or audit advice. Alignment with NIST CSF 2.0 and/or NIST AI RMF 1.0 should be independently verified as part of any formal compliance or assurance program. The AI RMF "program maturity band" shown in this dashboard is not an official NIST maturity model. See [Security](#security) above for known limitations around the Import feature.

## License

MIT — see [LICENSE](LICENSE). Fork it, modify it, use it commercially, no permission needed.
