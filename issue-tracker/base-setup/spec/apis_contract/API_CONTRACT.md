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

## Issue data and operations
DummyJSON posts provide sample issue text; these are demonstration issues. The old issue seed.json and UX text are illustrative, not the live dataset. Do not require exactly 12 remote issues.

| Operation | Public request and local behavior |
|---|---|
| List | GET /posts?limit=6&skip=0; subsequent skip increments by 6. Read posts,total,skip,limit. |
| Search | GET /posts/search?q=<encoded query>&limit=6&skip=0; retain q in the URL. |
| Detail | GET /posts/:id and GET /posts/:id/comments for a remote issue. A local- ID resolves from storage without a remote detail request. |
| Create | POST /posts/add with {title,body:description,userId:authenticatedUser.id}. On success save a uniquely identified local issue with priority, status Open, assignee null and empty comments. |
| Comment on remote issue | POST /comments/add with {body,postId:remoteIssueId,userId:authenticatedUser.id}; after acknowledgement save a local comment override. |
| Comment on local issue | Save a validated local comment directly; never POST an invented remote postId. |
| Edit or resolve remote issue | PATCH /posts/:id with {title:existingTitle,body:existingDescription}; this is the transport acknowledgement. Persist priority, assignee and status in the local override. Do not claim DummyJSON implements those domain fields. |
| Edit or resolve local issue | Validate and save directly to the browser store; no remote request to a simulated ID. |

Map post id,title,body to issue id,title,description. Initialize remote issues as Open, Medium, Unassigned, with updatedAt=2027-01-11T09:00:00Z unless a saved override exists. Comments map id,body,user.fullName (fallback username) with that same fixed initial timestamp because the endpoint does not supply the required date. Label initial dates consistently; do not invent varying remote timestamps. For new comments use the signed-in display name. Use the same frozen timestamp for saved domain mutations throughout this protocol.

Persist new issues separately and render them first, filtered by the submitted query against title/description. The remote list remains paginated six per request; local issues do not advance remote skip. Show loaded count against remote total plus matching local issue count. On a clean store the initial page has six remote issues. Retain fetched remote order, avoid duplicate IDs, and stop requests at remote total. Search remote results follow DummyJSON's search response; add locally created matches without fetching all remote posts. Existing titles/descriptions cannot be edited, so remote search membership is unaffected by domain overrides.

Priority is Low/Medium/High; assignee is null/Alex Morgan/Jamie Lee; status is Open/In progress/Resolved. Title creation requires trimmed 3–80 characters, description 10–1000, comments 1–500. Show resolution only through confirmation. Resolved issues cannot be reopened. Persist all saved attributes and comments across refresh and re-login. No delete, attachment, or title/description edit feature.
