# Next Priorities

These are proposed follow-up tasks based on the current implementation, not claims that the work is already complete.

1. **Align the dashboard period with its data.** Replace the hardcoded `2024 - Full Year` label in `frontend/src/App.tsx` with a range derived from the loaded data or explicitly selected by the user; add frontend tests for the chosen behavior.
2. **Define empty-data behavior.** Make `/api/metrics/facets` return a deliberate response for an empty movement set rather than indexing the first and last elements; add a backend test.
3. **Validate comparison date ranges.** Reject or document `start_date > end_date` for `/api/metrics/comparison`, and test both valid and invalid periods.
4. **Avoid changing global random state.** Refactor mock generation to use a local `random.Random(seed)` instance and keep seeded output deterministic with appropriate tests.
5. **Connect dashboard features intentionally.** If summary, facets, comparison, alerts, or B2B/B2C behavior is part of the product, wire the endpoint into the UI with loading, error, empty, and success states; otherwise keep those capabilities clearly documented as API-only.
6. **Harden financial edge cases.** Confirm frontend behavior for empty data and backend comparison results when the previous net is zero (`delta_pct: null`); add regression tests before changing calculations.
7. **Re-run relevant checks after changes.** Run backend pytest for API changes and, from `frontend/`, `npm test`, `npm run build`, and `npm run lint` as applicable.
