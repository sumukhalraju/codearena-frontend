# CodeArena frontend

This repository is the web client for CodeArena. It is nested in the parent repository `codearena` as the `frontend` submodule.

The parent repository is the rulebook. This repository renders those rules. It does not invent rounds, scores, timers, or endpoints. If a screen needs a behavior the parent docs do not describe, change the parent first.

Start with the parent files:

- `docs/00-start-here.md`
- `docs/01-product.md`
- `docs/09-frontend.md` (the build manual)
- `docs/06-http-api.md`
- `docs/07-realtime.md`
- `contracts/openapi.yaml`
- `contracts/constants.json`

`docs/09-frontend.md` in the parent is the page to follow while scaffolding. It names the stack, the routes, the arena layout, the state rules, and the failure copy. This README is the local entry so a clone of only this repository still points at that manual.

## Stack to scaffold

- Node.js 22, pnpm 9
- Next.js App Router, TypeScript strict (`strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`)
- Tailwind CSS
- Monaco through `@monaco-editor/react`, bundled
- TanStack Query for HTTP
- Zod on every response
- The platform `WebSocket`. Not Socket.IO
- Vitest and Playwright

## Commands, once `package.json` exists

```powershell
pnpm install
pnpm dev
```

The dev server listens on port 3000. The API is `http://localhost:4000`. Copy `.env.example` to `.env.local`.

```powershell
pnpm typecheck
pnpm test
pnpm exec playwright test
```

Production image uses Next `output: 'standalone'`.

## Hard rules

- The access token stays in memory. The refresh token is an `HttpOnly` cookie set by the API. This app never writes either to `localStorage`.
- Submit goes to REST with an `Idempotency-Key` created before the request and reused if that request is retried.
- The socket is for server push. A dropped socket does not block submit. Poll `GET /matches/{id}` until the socket is back.
- Do not show opponent source, the correct option, or hidden tests during an active round. Those fields arrive only in the payloads the realtime doc allows.
- Do not compute the official score on the client.
- Vendor `contracts/` from the parent when the app is scaffolded, generate types with `openapi-typescript`, and commit the generated file. The parent script `scripts/check-contracts.ps1` fails if the vendored copy drifts.

## Routes

`/`, `/login`, `/signup`, `/forgot-password`, `/reset-password`, `/dashboard`, `/play/quick`, `/play/queue`, `/lobby/new`, `/lobby/join`, `/lobby/{code}`, `/match/{id}`, `/match/{id}/result`, `/players/{username}`, `/leaderboard`, `/me/history`, `/me/settings`, `/setter/problems`, `/setter/problems/new`, `/setter/problems/{id}`.

The arena layout and the empty states are specified in the parent frontend doc, not restated here, so they cannot fork.

## What is not in this repo yet

There is no application code. Milestone 0 in the parent `docs/14-delivery.md` is the first scaffold.
