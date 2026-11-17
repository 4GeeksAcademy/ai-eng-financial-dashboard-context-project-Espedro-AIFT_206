# Proposed Financial Dashboard Rules

These rules are proposed contributor and agent guidance based on the current repository structure and observed behavior. Follow them for new work unless superseded by a more specific project instruction.

## 1. Read project guidance before making changes

- Before analyzing, modifying, or generating project files, review `AGENTS.md` and the current contents of `.agents/rules/`, `.agents/skills/`, and the project memory bank when present.
- Repository paths are case-sensitive on Linux. Use the exact directory casing present in the workspace; do not treat `Memory-Bank/` and `memory-bank/` as interchangeable.

## 2. Preserve frontend/backend API contracts

- Keep backend request and response contracts explicit using the existing FastAPI `Query` validation, `Literal` value types, Pydantic models, and route `response_model`s.
- When changing an API field, enum value, or response shape, update the corresponding TypeScript types in `frontend/src/lib/financial-types.ts` and all affected consumers in the same change.
- Preserve the API's `outcome` wire-format terminology unless intentionally making a coordinated breaking contract change. User-facing text may use clearer language such as expenses or expenditure.
- Keep browser requests relative (for example, `/api/metrics`) and use `VITE_API_BASE_URL` for a non-default API origin. Do not hardcode `localhost` into browser API requests; local development routes `/api` through the Vite proxy to the Compose backend service.

## 3. Keep responsibilities in the established layers

- Keep frontend financial calculations and formatting in `frontend/src/lib/financial-utils.ts` rather than embedding them in presentational dashboard components.
- Keep dashboard rendering in `frontend/src/components/dashboard/` and shared UI primitives in `frontend/src/components/ui/`.
- Backend route handlers and current filtering/aggregation helpers live in `backend/app/routes.py`. If that module grows substantially, extract helpers into an appropriate module without changing route behavior unintentionally.

## 4. Treat generated data as demo data

- Do not represent generated mock movements as persistent, authoritative, or real financial records.
- Preserve deterministic test behavior where needed, but avoid mutating Python's process-wide random state. Prefer a local `random.Random(seed)` instance when modifying mock generation.
- The mock-data year is calculated relative to the current date and can span two calendar years. Avoid tests or UI copy that assume a fixed year unless the clock or data range is explicitly controlled.
- Keep dashboard date labels consistent with the data actually displayed. Do not leave fixed period text such as `2024 - Full Year` in place if the underlying date range changes.

## 5. Handle empty and invalid metric inputs deliberately

- Define and test behavior for empty datasets before adding data sources or filters that can produce no movements. In particular, do not assume `build_metrics_facets` can process an empty list; its current implementation accesses the first and last item.
- Validate period boundaries where appropriate, including rejecting or defining behavior for `start_date > end_date`.
- Handle nullable comparison percentages. The comparison API returns `delta_pct: null` when the previous period's net value is zero; frontend code must not assume it is always numeric.
- Keep financial calculations and rounding semantics consistent across API and UI layers. Add tests for boundary cases when changing those calculations.

## 6. Wire dashboard features to the API explicitly

- Do not assume that an API endpoint affects the dashboard merely because it exists. `App.tsx` currently fetches raw `/api/metrics`; wire summary, comparison, alerts, facets, or business-type endpoints into frontend state and UI before relying on them in the user experience.
- When adding an API-backed feature, cover loading, error, empty, and successful response states in the UI as appropriate.

## 7. Test and validate changes

- For backend route, filter, and aggregation changes, add or update tests in `backend/tests/test_routes.py`.
- For frontend calculations or formatting changes, add or update tests in `frontend/src/lib/financial-utils.test.ts`.
- Run the relevant backend tests and, from `frontend/`, use `npm test`, `npm run build`, and `npm run lint` as applicable.
- Keep test expectations independent of the current calendar date unless the date is fixed in the test.

## 8. Follow local code style

- Match the style of the surrounding file because formatting is not fully uniform across the repository.
- Do not apply broad formatting-only changes alongside functional changes unless requested or required by an adopted formatter.
