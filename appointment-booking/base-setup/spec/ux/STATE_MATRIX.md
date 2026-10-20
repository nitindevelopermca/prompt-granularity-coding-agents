# Required UI states

| Surface | Required states and recovery |
|---|---|
| Login | Idle, show/hide password, empty-field errors, pending/disabled, wrong credentials, network error/retry |
| List/search | Loading, six initial records, load-more pending, complete list, empty dataset, no matches, request failure preserving prior data |
| Detail | Loading, populated, 404, retryable request failure, route/back behavior |
| Create/comment/draft | Labeled constraints, invalid/empty input, pending, success, failed submit preserving input |
| Update | Saved view, unsaved edits, pending, failure retaining saved state and edits, persisted success |
| Confirmation dialog | Focus entry/trap/return, Escape/cancel without write, disabled pending actions, error, success |
| Booking only | Unavailable slot, no available slots, stale selection/local availability conflict, restore draft, reschedule conflict, retained cancelled record |

UX images show the normal flow and confirmation dialogs. This matrix makes error and empty states mandatory even when not separately illustrated. Do not add evaluator fault controls to product screens.


Session restoration must retain pathname and query parameters; draft review also retains service, slot and notes. Public request failures and browser storage failures are distinct and must preserve the last saved view.
