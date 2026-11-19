# Product Context

## Purpose

The Financial Metrics Dashboard is a demo web application for exploring a business's financial movements. It presents high-level income, outcome (expenses), profit, and profit-margin metrics, plus monthly income-versus-outcome and profit-percentage charts.

## Users and use cases

- Users view a compact financial overview in a browser.
- Contributors use the API and dashboard as a project for adding and validating financial reporting features.
- The dashboard currently has no authentication, tenant model, or persistent storage and must not be treated as a production accounting system.

## Current behavior

- The frontend loads movements from `GET /api/metrics`, computes KPI and monthly chart values in the browser, and renders the dashboard.
- The backend returns deterministic, seeded synthetic movements. Other endpoints expose facets, grouped summaries, top categories, period comparison, outcome alerts, and B2B/B2C movement subsets.
- The API uses `outcome` for expense operations; preserve that term in the wire contract unless making a coordinated API and frontend change.
- The dashboard's displayed period is currently hardcoded to `2024 - Full Year`, while mock dates depend on the current date. Treat this as a known consistency issue, not a guarantee about the data range.

## Product boundaries

- All generated movements are demonstration data, not real or durable financial records.
- The dashboard currently consumes only the raw metrics endpoint; the existence of additional endpoints does not mean their features are visible in the UI.
- Do not imply financial advice, audited balances, or data persistence.
