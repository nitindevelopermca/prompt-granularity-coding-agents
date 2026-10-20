# Canonical work units

## Work unit 1 - Login and session

Implement /login with labeled username and password fields, show/hide password, and Sign in. Use POST https://dummyjson.com/auth/login and the response/session mapping in API_CONTRACT.md. Show pending, required-field, invalid-credential and network-error states. On success retain the mapped user, accessToken and refreshToken in app-scoped localStorage and navigate to /services. Retain authentication and the exact current pathname and query string after browser refresh. Do not send an authenticated user back to the home/list page on reload. Read stored session state before route guards decide where to navigate; show a restoring state while validating it. Retain search query parameters. Restore booking review with the same service, slot and notes. A signed-out direct protected URL redirects to login; signing out clears the session and booking draft. Saved domain records are scoped to application and user and remain available on signing back in. A temporary network failure must not erase a stored session or silently reset the route. A direct protected URL without a session must redirect to /login. Do not add signup or password recovery.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 2 - Authenticated application shell

Create the shared Slotwise header, Services and My appointments navigation, signed-in user name and Sign out. Sign out clears the session and booking draft, returns to /login and protects all application routes. Add route handling, document title, meta description, semantic landmarks and visible focus. Service detail, booking review and appointments destinations may be placeholders until assigned.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 3 - Paginated service loading

At /services load the first six fixed catalogue services with provider names from GET https://dummyjson.com/users?limit=6&skip=0&select=id,firstName,lastName, using API_CONTRACT.md. Render the returned count and Load more to append the next six records without duplicates. The fixed frontend catalogue has 12 services; the second page fetches provider IDs 7–12. Do not use the public API total user count as the service total. Show loading, empty, retry and end-of-list states. Keep already displayed services on a failed load-more request. No prefetching the entire collection.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 4 - Service cards and visual hierarchy

Render each service title, provider, duration in minutes and short description from the fixed catalogue; obtain provider display names from DummyJSON. Add View times linking to /services/:id. No prices, payments or invented availability. Follow the supplied service-card UX: three columns on desktop, stacked below 768 px, no horizontal page overflow at 390 px. The detail destination may remain a placeholder until work unit 6.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 5 - Service search

On form submit filter the fixed catalogue by title/description, paginate matching services six at a time and fetch their provider names from GET https://dummyjson.com/users/:providerId as specified in API_CONTRACT.md. Trim query and retain it in the URL. Paginate search using the same query. Show no-results feedback and keep current cards on failure. Clearing search restores the previously loaded unfiltered list and pagination state within the session; refresh may reload from the first page. Ignore stale responses so an old request cannot overwrite a later query.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 6 - Service detail and slot availability

At /services/:id resolve the fixed service and slots, fetch its provider from GET https://dummyjson.com/users/:providerId, and calculate availability from the current persisted appointments. Display description, provider, duration and selectable available slots; unavailable slots are disabled and labeled Unavailable. All dates/times display explicitly in UTC, using the frozen study clock 2027-01-11T09:00:00Z rather than the operating-system date to judge future availability. Include loading, retry, no-available-slots, not-found and Back to services states. Selection alone must not create an appointment.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 7 - Booking draft and review

Require one available slot before Continue. Allow optional notes, trimmed maximum 500 characters. Store serviceId, slotId and notes as a booking draft in localStorage, then navigate to /booking/review. Show service/provider/duration/date/time UTC and notes. Refresh restores the same draft and route. Re-read local appointments to check availability on review; an unavailable selection requires reselection and must not silently switch slots. Edit selection returns to service detail with draft values; Cancel booking clears the draft and returns to Services. Do not create an appointment yet.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 8 - Confirm appointment

On review, Confirm booking rechecks availability against stored appointments and commits {id,serviceId,slotId,notes,status,createdAt} to user-scoped localStorage according to API_CONTRACT.md. There is no remote booking endpoint. Disable repeat submissions while pending. Success shows the locally allocated booking reference, clears the draft and offers View my appointments. A failure retains the draft and does not show success. A local availability conflict means the slot is unavailable: refresh available slots and require explicit reselection. Do not expect an HTTP 409 from DummyJSON for a booking. Create exactly one appointment for a double click. Do not add payment, email, SMS or calendar integrations.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 9 - My appointments and rescheduling

At /appointments load user-scoped persisted appointments and show each service title, provider, date/time UTC, notes, reference and status; resolve service/slot fields through the contract. Preserve this route on refresh. Confirmed appointments offer Reschedule: show available slots for the same service, select a different slot and explicitly Save the new slotId to browser storage after rechecking availability. On success update the displayed slot; on a storage failure or local slot conflict preserve the prior booking and offer retry/reselection. Include list loading/retry/empty states. Cancelled records remain visible without rescheduling controls.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.

## Work unit 10 - Cancel appointment with confirmation

For confirmed appointments add Cancel appointment, opening an accessible dialog with the booking reference and date/time UTC. Keep booking dismisses without changing storage. Confirm commits status Cancelled to the stored appointment array. Only after success mark it Cancelled, retain it in the list, disable further mutation actions and announce success. On failure retain Confirmed and offer retry. Cancellation survives refresh and releases the slot for a later booking. Do not delete the record.

Apply the shared accessibility, error handling, persistence and scope rules to this work unit. Implement only assigned units; preserve completed behavior.
