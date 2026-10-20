# Public API and browser persistence contract — protocol v1.1

## Execution
Base URL: https://dummyjson.com. No API key, account registration, local API process, or custom backend is required. Use browser fetch directly with JSON requests. Do not call the retired /api/* paths as HTTP endpoints. Internal application functions are allowed, but they must implement this contract, not assume an absent server.

## Authentication
- POST /auth/login with {username,password,expiresInMins:60}. Demo credentials: emilys / emilyspass.
- The response has top-level id, firstName, lastName, username, accessToken and refreshToken. Map the display user to {id,name:firstName + " " + lastName}; there is no nested response.user to assume.
- Persist user and tokens under an application-specific key: trackline:v1.1:session or slotwise:v1.1:session. Never save the password. This is a demonstration app using public demo accounts.
- GET /auth/me with Authorization: Bearer <accessToken> validates the session. If that returns 401, POST /auth/refresh with {refreshToken,expiresInMins:60}, persist replacement tokens, and retry once. Clear session and require login if refresh is rejected; do not loop. A network/5xx failure offers retry while retaining the session and requested URL. Do not redirect to the list just because authentication was restored.
- Sign out clears that application's session and draft. Never use localStorage.clear(), which would erase the other application's data.

## Persistence and errors
DummyJSON's documented mutations simulate responses; they do not persist server changes. The browser is authoritative for successful local edits and bookings. Use app/user-scoped keys such as trackline:v1.1:user:<id>:state and slotwise:v1.1:user:<id>:state. Merge persisted overrides over remote records; a subsequent GET must not undo a saved edit. Use unique local IDs (local-<UUID>) for created issues/comments and numeric locally allocated booking IDs. Store any remote acknowledgement ID separately; never treat a simulated create response ID as a persistently retrievable server record.

For a public API mutation, commit local state only after a successful response. If localStorage writing fails, preserve the previous saved state and editable input and show retryable feedback; do not show success. Local-only booking writes also require a successful storage commit before success. Serialize pending actions to prevent double-click duplication. Re-read current storage before booking/conflict checks. These are same-browser demonstration records, not cross-device reservations.

Check HTTP response.ok and show useful errors. A remote 404 gets a not-found state, not a fake successful record. Keep current data after failed requests. A storage parsing error must be surfaced with recovery guidance; do not silently overwrite existing records with seed data.

## Refresh and routing
Retain authentication and the exact current pathname and query string after browser refresh. Do not send an authenticated user back to the home/list page on reload. Read stored session state before route guards decide where to navigate; show a restoring state while validating it. Retain search query parameters. Restore booking review with the same service, slot and notes. A signed-out direct protected URL redirects to login; signing out clears the session and booking draft. Saved domain records are scoped to application and user and remain available on signing back in. A temporary network failure must not erase a stored session or silently reset the route.

Browser persistence is required for refresh on the same origin/browser profile. Different ports/origins have separate browser storage. Retain query strings on back navigation. Initialize from window.location rather than resetting to a default page on every mount.

## Public documentation
- https://dummyjson.com/docs/auth
- https://dummyjson.com/docs/posts
- https://dummyjson.com/docs/comments
- https://dummyjson.com/docs/users

## Service catalogue, providers, and slots
Use spec/fixtures/seed.json for the 12 coaching service titles, descriptions, durations, fixed slots, and the initial appointment. This JSON is bundled frontend data, not a running service. Each service now has providerId equal to its id. Fetch provider names from DummyJSON and do not assume its users are actual service providers.

| Operation | Public request and local behavior |
|---|---|
| Initial service page | GET /users?limit=6&skip=0&select=id,firstName,lastName. Join returned IDs 1–6 to the fixed services by providerId. |
| Second service page | GET /users?limit=6&skip=6&select=id,firstName,lastName. Join IDs 7–12. The study catalogue total is 12, not the API's total users. Stop at 12. |
| Service search | Filter all 12 fixed catalogue titles/descriptions by a trimmed case-insensitive substring, paginate matching services six at a time, then GET /users/:providerId for provider names on that page. Preserve q in the URL. A failed provider request retains the previous list and offers retry. |
| Service detail | Resolve the catalogue ID locally and GET /users/:providerId for its display name. Unknown catalogue IDs have a not-found view. |
| Slot availability | Read fixed slots for the selected service; calculate availability from locally persisted Confirmed appointments. No HTTP slots endpoint. |
| My appointments | Read the user-scoped local appointment array; resolve service/slot fields from the fixed catalogue and provider names from GET /users/:providerId. |
| Confirm, reschedule, cancel | Local validation and localStorage write only. DummyJSON has no appointment or reservation endpoint. Do not send booking requests to invented public URLs or unrelated todos/carts endpoints. |

Provider display is firstName + lastName. Do not display the historic provider names in the UX reference images as authoritative. Network loading/retry states apply to provider requests, while local storage failures apply to booking writes.

On first use for each demo user, initialize appointments from seed exactly once. An empty saved array remains empty: never reseed on every refresh. Services have {id,title,providerId,durationMinutes,description}; slots have {id,serviceId,startUtc}; appointments have {id,serviceId,slotId,notes,status,createdAt}. Allocate appointment IDs as 1 + maximum existing numeric id; new records appear first. Slot 101 starts occupied by appointment 1. Frozen study clock: 2027-01-11T09:00:00Z. Show UTC explicitly and do not use today's device clock to discard fixture slots.

At confirm/reschedule, re-read stored appointments before checking conflicts and saving. A slot admits at most one Confirmed appointment in the current browser store. An unavailable selection keeps the draft/previous appointment and requires explicit reselection. Save the entire updated array in one localStorage operation. Reschedule stays within the same service and releases the prior slot only after a successful commit. Cancel retains the record as Cancelled and releases its slot; cancelled records cannot be modified further. This is a local conflict check, not an HTTP 409 or a cross-device concurrency guarantee.

The draft {serviceId,slotId,notes} is user-scoped and persisted. Notes trim to at most 500 characters. Review rechecks local availability. Cancel draft/sign out clears it; successful booking clears it after committing the appointment. Never clear it on a failed booking write. No payments, medical workflows, calendars, reminders or real personal information.
