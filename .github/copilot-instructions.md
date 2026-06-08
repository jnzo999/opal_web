```markdown
# Copilot / AI Agent Instructions — workspace summary

Purpose: Help an AI agent become productive quickly in this workspace (two related projects live here).

- **Projects present**: `Mjolnir/` (fullstack Scrumban app: `client/` React + `server/` Node/Express/Mongoose) and `opal_web/` (static pages and assets). Focus most edits on `Mjolnir/` unless the task explicitly names `opal_web/`.

- **Run & dev commands (exact)**:
  - Install all deps: run `npm run install-all` from `Mjolnir/` (runs installs for root, `server`, and `client`).
  - Run both apps locally: from `Mjolnir/` run `npm run dev` (uses `concurrently` to start `server` and `client`).
  - Server dev: `cd Mjolnir/server && npm run dev` (uses `tsx watch src/index.ts`).
  - Client dev: `cd Mjolnir/client && npm run dev` (Vite).
  - Build client: `cd Mjolnir/client && npm run build`.
  - Run server tests: `cd Mjolnir/server && npm test` (Jest + Supertest).

- **Key architectural boundaries** (must be preserved):
  - `server/controllers/*` -> only translate HTTP <-> service calls.
  - `server/services/*` -> business logic only; call DAL but do not access models directly.
  - `server/dal/*` -> only persistence code importing Mongoose models from `server/models/*`.
  - Controllers must NOT import models or perform DB ops; DAL must NOT contain business logic.

- **Code style & constraints**:
  - Server uses ESM (`"type": "module"`) — local relative imports must include the `.js` extension in built code.
  - TypeScript: avoid `any`. Client mirrors backend models under `client/src/types/`.
  - Client styling: Tailwind only; look in `Mjolnir/client/src/` for components, hooks and services.

- **Domain rules you must preserve** (explicit logic examples):
  - Status inheritance: changing a `UserStory` status triggers updates to its parent `Feature` and grandparent `Epic` (see `server/services/StatusService`).
  - Sprint migration: an hourly job (bootstrapped in `server/src/index.ts`) moves incomplete stories from expired sprints; tests assert this side-effect.
  - Auth gate: every protected route must check `isApproved === true` on the user (middleware in `server/middleware/auth.ts`).

- **Where to change what** (quick map):
  - API endpoints: `Mjolnir/server/controllers/*` and `Mjolnir/server/routes/*`.
  - DB logic: `Mjolnir/server/dal/*` and `Mjolnir/server/models/*`.
  - Frontend API wiring: `Mjolnir/client/src/services/api.ts` and domain hooks in `client/src/hooks/`.
  - UI components: `Mjolnir/client/src/components/` and `pages/` for route-level changes.

- **Testing & CI expectations**:
  - Server: Jest + Supertest; tests must assert side-effects (status inheritance, sprint migration).
  - Policy: PRs that touch server endpoints expect tests to run; historically `npm test` is required pre-merge.

- **Common pitfalls for agents**:
  - Do not refactor across layers in one change (e.g., moving DB calls from DAL into Service) — keep edits local to layer boundaries.
  - When editing imports in server TypeScript output, add `.js` extension for relative imports to satisfy ESM runtime.
  - Respect client-only constraints (no Redux, no WebSockets). Use provided hooks & contexts.

- **Helpful files to read before coding**:
  - `Mjolnir/AGENTS.md`, `Mjolnir/server/AGENTS.md`, `Mjolnir/client/AGENTS.md` — project-specific rules and examples.
  - `Mjolnir/server/index.ts` — app bootstrap (contains scheduled migration job).
  - `Mjolnir/server/controllers/*`, `Mjolnir/server/services/*`, `Mjolnir/server/dal/*` — canonical layer examples.
  - `Mjolnir/client/src/services/api.ts`, `Mjolnir/client/src/hooks/` — how the frontend talks to backend.

When unsure: open the matching `AGENTS.md` in `Mjolnir/` (server or client) and follow its explicit rules; if gaps remain, ask a human reviewer and reference the exact file(s) you changed.

```
