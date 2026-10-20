# Trackline blank starter
React + TypeScript + Vite. Read spec/SPEC_FREEZE.md first.

## Manual commands — operator only
Run npm ci when preparing a fresh build. After implementation, you can manually run npm run dev and open the URL printed by Vite (configured port 8080). Stop it yourself with Ctrl+C. No backend process is needed.

The operator installs dependencies and starts/stops the application manually. Do not run npm run dev, npm start, npm run preview, a local API/backend, a browser preview, or any background server. Do not run lint, ESLint, npm run lint, or install lint tools. Keep dev/preview scripts available for the operator. No automatic startup hooks. No new dependencies or changes to package.json/package-lock.json. The common final validation permits build and typecheck only. Do not explore Git repositories, Git metadata, remotes, or history without the operator's explicit authorization.

Demo sign-in: emilys / emilyspass using DummyJSON. The supplied App.tsx intentionally returns null; implementing the scheduled work units creates the application.
