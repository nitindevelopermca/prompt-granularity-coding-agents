# Slotwise specification freeze - public API protocol v1.1

## Authority and scope
This file, API_CONTRACT.md and WORK_UNITS.md define requirements. UX pictures illustrate layout; they do not override textual requirements or API data. This package is a proposed new research protocol, not a retroactive change to the e-commerce study. No outcomes or preferred schedule are assumed.

## Technology and execution
Use the supplied Vite/React/TypeScript starter and preserved lockfile. Use https://dummyjson.com according to API_CONTRACT.md. Demo login: emilys / emilyspass. No backend is required. Routes: /login; /services; /services/:id; /booking/review; /appointments. Implement URL navigation with the existing dependencies; React Router is not required.

The operator installs dependencies and starts/stops the application manually. Do not run npm run dev, npm start, npm run preview, a local API/backend, a browser preview, or any background server. Do not run lint, ESLint, npm run lint, or install lint tools. Keep dev/preview scripts available for the operator. No automatic startup hooks. No new dependencies or changes to package.json/package-lock.json. The common final validation permits build and typecheck only. Do not explore Git repositories, Git metadata, remotes, or history without the operator's explicit authorization.

## Functional requirements
Implement the ten canonical work units as assigned by the schedule. Follow the public API mappings and local persistence rules in API_CONTRACT.md. Successful changes survive refresh; public API acknowledgements alone do not provide persistence. Keep valid data and editable inputs after a failure. Disable duplicate submissions while pending. Search is submitted explicitly. Use the frozen study clock 2027-01-11T09:00:00Z for domain dates.

Retain authentication and the exact current pathname and query string after browser refresh. Do not send an authenticated user back to the home/list page on reload. Read stored session state before route guards decide where to navigate; show a restoring state while validating it. Retain search query parameters. Restore booking review with the same service, slot and notes. A signed-out direct protected URL redirects to login; signing out clears the session and booking draft. Saved domain records are scoped to application and user and remain available on signing back in. A temporary network failure must not erase a stored session or silently reset the route.

## UX and accessibility
Use a white/light neutral surface, dark navy text and teal primary actions, with the supplied screen references. Desktop reference is 1440 px; mobile target is 390 px. Below 768 px stack content/cards/forms, retain named navigation and avoid horizontal page overflow. Provide semantic header/nav/main, one main heading, meaningful page titles and descriptions, labeled inputs, keyboard operation and visible focus. Form errors are associated with fields. Pending and feedback text is announced through appropriate status/error regions. Status badges include text. Dialogs have accessible names, move and trap focus, close with Escape when not submitting, and return focus to their trigger. Escape/cancel must not mutate data. Use sufficient text contrast and don't rely on color alone.

## States and exclusions
All applicable screens cover loading, empty, success, validation error, request error and retry; see ux/STATE_MATRIX.md. No signup, password recovery, production authorization, multi-tenancy, file attachments, analytics dashboards or extra business features. No medical workflows, prices/payments, notifications, external calendars or timezone switching.

## Measurement boundaries
Audit pages: /login, /services, /appointments. The operator defines the common browser/Lighthouse settings and test procedure outside the agent prompt. A blocked or inferred outcome is recorded separately, never disguised as an independently executed failure. No missing audit is replaced with zero. Twenty-five cases are specified for evaluation; identical counts do not imply identical difficulty across applications.
