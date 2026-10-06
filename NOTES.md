# NOTES

## Summary of changes
Detailed explanations are in the `handwritten/` folder.

1. **Search SQL** (`TaskRepository.java`): `AND`/`OR` were mixed without brackets, so the status filter was ignored and archived tasks leaked into results. Added brackets.
2. **Artificial delay** (`TaskController.java`): removed a `Thread.sleep` on every request (about 1.2 s down to 0.14 s).
3. **Validation** (`TaskController.java`): invalid `status`, `page` or `pageSize` now return 400 instead of 500.
4. **Page reset** (`App.jsx`): page returns to 1 when the search or status changes, so the table is not left empty.
5. **Data fetching** (`useTasks.js`): stale responses are ignored, loading always ends, and old errors are cleared.
6. **Debounce** (`useDebounce.js`, `App.jsx`): search waits 300 ms after typing stops (9 requests down to 1 for "logging").

## What I chose not to change
- `%` and `_` typed in search act as LIKE wildcards. Low impact.
- `api.js` shows "Request failed: 400" and ignores the backend's error message.
- The `open-in-view` warning, and the Oracle file in `db/` (it does not run locally, so I did not review it).
- The double request on first load is React StrictMode in development only.
- No rewrite of the app.

## Biggest remaining risk
Pagination happens in Java memory: the backend loads every matching task and slices it with `subList`. Fine for about 50 rows, but it will not scale. It should move to database pagination (`Pageable` or `LIMIT/OFFSET`).

## Tools and AI used
I used Claude to help read the code, find the bugs and understand their causes. I made the edits, tested each one with the browser and `curl`, and wrote the handwritten explanations myself.

## Assumptions
- Case-insensitive search is intended.
- Archived tasks should never be shown.
- A maximum page size of 100 is acceptable.
