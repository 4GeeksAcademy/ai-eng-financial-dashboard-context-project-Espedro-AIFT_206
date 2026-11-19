# Project Verification Summary

## Project overview

This repository contains a financial metrics dashboard built with a React + TypeScript frontend and a FastAPI backend. It displays income, expenses (called "outcome" in the API), profit, profit margin, and monthly income-versus-outcome charts.

## Architecture and data flow

- The browser entry point is `frontend/src/main.tsx`, which mounts the React application from `frontend/src/App.tsx`.
- `App.tsx` fetches financial movements from `GET /api/metrics` and passes them to the dashboard components.
- `frontend/src/lib/financial-utils.ts` calculates KPI totals and monthly chart data from the fetched movements.
- Dashboard UI components are in `frontend/src/components/dashboard/`; shared UI primitives are in `frontend/src/components/ui/`.
- The backend entry point is `app.main:app` (configured in `backend/app/main.py` and launched with Uvicorn). `main.py` configures CORS and registers the router from `backend/app/routes.py`.
- The backend generates deterministic seeded mock financial movements; the project currently has no database configured.

## Backend API

Routes are defined in `backend/app/routes.py`:

- `GET /health`
- `GET /api/metrics` — financial movements, with optional date, category, and operation-type filters
- `GET /api/metrics/facets` — available filter values and date range
- `GET /api/metrics/summary` — grouped income, outcome, and net totals
- `GET /api/metrics/categories/top` — highest-total categories
- `GET /api/metrics/comparison` — current and previous period net comparison
- `GET /api/metrics/alerts` — outcome increase alerts
- `GET /api/metrics/b2b` and `GET /api/metrics/b2c` — movements scoped by business type

FastAPI's interactive API documentation is served at `/docs`.

## Services and local run

`docker-compose.yml` defines two services:

- **frontend:** Vite development server on port `5173`; `/api` is proxied to `http://backend:8000` by `frontend/vite.config.ts`.
- **backend:** FastAPI/Uvicorn on port `8000`, with debugpy exposed on port `5678`.

Start the services with:

```bash
docker compose up --build
```

Local URLs:

- Frontend: `http://localhost:5173`
- Backend: `http://localhost:8000`
- API docs: `http://localhost:8000/docs`

## Tests

- Backend API and logic tests are in `backend/tests/test_routes.py`.
- Frontend financial utility tests are in `frontend/src/lib/financial-utils.test.ts`.
- Frontend package scripts include `npm test`, `npm run build`, and `npm run lint` (run from `frontend/`).

## Findings by category

### Agent and repository guidance

- `AGENTS.md` directs contributors to inspect `./.agents/rules`, `./.agents/skills`, and `./memory-bank` before acting. The financial dashboard rule and memory bank are present; `.agents/skills/` currently has no skill files.
- Repository guidance expects the lowercase `memory-bank/` path. Linux paths are case-sensitive; the earlier `Memory-Bank/` capitalization did not satisfy that instruction. This verification note is kept in the required lowercase directory.

### Architecture and implementation conventions

- **API contracts:** Backend query options use `Literal` aliases for supported enum-like values, Pydantic models for response shapes, FastAPI `response_model`s, and `Query` for defaults/validation. Update TypeScript data types in `frontend/src/lib/financial-types.ts` when changing API payloads.
- **Separation of concerns:** Frontend financial aggregation and formatting helpers live in `frontend/src/lib/financial-utils.ts`; dashboard components render their typed results. Backend filtering and aggregation helpers are currently colocated with routes in `backend/app/routes.py`.
- **Terminology:** API/type contracts use `outcome` for expenses, while user-facing copy may say expenditure/expenses. Preserve the wire-format name unless deliberately changing the backend and frontend contract together.
- **API routing:** The frontend uses `VITE_API_BASE_URL` when provided and otherwise relative `/api/...` URLs. Vite proxies `/api` to the Compose service `backend:8000`; avoid hardcoding `localhost` in browser requests.
- **Formatting:** Frontend files mix quote/semicolon styles and Python formatting is not consistently normalized. Follow nearby code style unless a formatter is adopted.

### Testing and validation

- Add/update backend tests in `backend/tests/test_routes.py` for route, filtering, and aggregation changes; add/update frontend utility tests in `frontend/src/lib/financial-utils.test.ts` for calculation changes.
- Frontend commands, run from `frontend/`, are `npm test`, `npm run build`, and `npm run lint`.
- Keep changes focused and run the relevant tests/lint/build checks after edits.

### Data and product behavior risks

- **Synthetic data only:** Each endpoint regenerates mock movements using seed `42`; this is demo data, not real or persistent financial data. `generate_mock_movements` calls `random.seed`, changing Python's process-wide random state. A local `random.Random(seed)` instance is safer if extending this toward production-like behavior.
- **Date range shifts over time:** `_year_for_month` assigns months before the current month to this year and the current/future months to the previous year. The 12 month buckets can span two calendar years, and seeded records can map to different dates over time. Avoid tests assuming a fixed year unless the clock is controlled.
- **Hardcoded dashboard period:** `DashboardHeader` receives `2024 - Full Year`, while generated API dates depend on the current date. Keep the label synchronized with the actual data source/range.
- **Unused API capabilities:** Summary, comparison, alerts, facets, and B2B/B2C endpoints exist but `App.tsx` currently consumes only raw `/api/metrics`. Do not assume the extra endpoints affect the visible dashboard until wired in.

### Edge cases and API robustness

- `build_metrics_facets` indexes the first and last movement and will fail on an empty list; decide how empty data should be represented and cover it if data sources can return no movements.
- Period comparison does not validate that `start_date <= end_date`; validate input if the endpoint is exposed to arbitrary users.
- Comparison percentage change is `null` when the previous net is zero; clients must handle this explicitly.
