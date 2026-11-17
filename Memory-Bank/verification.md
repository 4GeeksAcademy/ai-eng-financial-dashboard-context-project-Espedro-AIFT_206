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
