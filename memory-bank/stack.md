# Technical Stack

## Frontend

- React 19 with TypeScript.
- Vite 8 development server and build tool.
- Tailwind CSS 4, Recharts, and small shared UI components.
- Source code is under `frontend/src/`; API-facing types and financial calculations are in `frontend/src/lib/`.
- The browser uses relative `/api` requests by default. Vite proxies these requests to `http://backend:8000`; `VITE_API_BASE_URL` can override the origin.

## Backend

- Python with FastAPI, Pydantic response models, and Uvicorn.
- Application entry point: `backend/app/main.py`; API routes and current mock-data/filter/aggregation helpers: `backend/app/routes.py`.
- The current implementation generates seeded synthetic data in memory; no database or persistence layer is configured.

## Local services

`docker-compose.yml` runs the frontend on port 5173 and backend on port 8000. The backend also exposes debugpy on port 5678. Start the stack with:

```bash
docker compose up --build
```

## Tests and validation

- Backend tests: `backend/tests/test_routes.py` (pytest).
- Frontend tests: `frontend/src/lib/financial-utils.test.ts` (Vitest).
- From `frontend/`, available checks are `npm test`, `npm run build`, and `npm run lint`.
