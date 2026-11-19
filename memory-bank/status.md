# Project Status

## Current state

- The repository is a React + TypeScript financial dashboard with a FastAPI backend.
- The frontend dashboard fetches `GET /api/metrics` and derives KPI and monthly chart data using `financial-utils.ts`.
- The backend exposes health, movements, facets, summaries, category rankings, comparisons, alerts, and B2B/B2C endpoints, backed by generated deterministic demo records.
- The repository has backend route tests and frontend financial calculation tests.
- Agent contribution rules are in `.agents/rules/financial-dashboard.md`.
- This memory bank uses the exact lowercase `memory-bank/` directory required by `AGENTS.md`; paths are case-sensitive on Linux.

## Known risks and gaps

- `App.tsx` displays the fixed period label `2024 - Full Year` although backend mock dates are calculated relative to today's date.
- The API's extra summary/filter/comparison/alert capabilities are not currently connected to the dashboard UI.
- `build_metrics_facets` assumes a non-empty movement list and accesses its first and last records.
- Period comparison does not currently validate that `start_date` is not after `end_date`.
- Comparison `delta_pct` can be `null` when the previous period net is zero.
- `generate_mock_movements(seed=...)` resets Python's process-wide random state.
- The mock date assignment can span two calendar years and changes with the current date.

See `verification.md` for the detailed verified architecture, contracts, and observations.
