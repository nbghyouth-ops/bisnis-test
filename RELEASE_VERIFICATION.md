# Release verification — current archive

**Release status: NOT PRODUCTION READY.** This is a source hardening/recovery candidate. External Neon provisioning, Neon Managed Better Auth, Object Storage, Neon Functions/Function Triggers, Android and real provider smoke tests have not been completed.

## What was inspected
- Root-flat Next.js application layout (`package.json`, `src/app`, `next.config.ts` at root).
- Existing 17-module / 16-template product structure and prior acceptance/blocker documentation.
- Historical test report claiming 107/107 checks; this was not rerun against this revision.
- Hardcoded localhost database URL in old `drizzle.config.json` (removed and replaced by env-based `drizzle.config.ts`).
- Runtime database DDL in `withTenant`/`withSystem`/health (moved to explicit `npm run db:bootstrap`).
- Public worker tick endpoint (now requires generated `CRON_SECRET`).
- Auth local fallback (now fails closed in production until Neon Managed Auth is integrated).
- Workspace API rate limit and body parsing path.

## Changes in this archive
- Vercel root-flat configuration and root preflight check.
- No Vercel Cron registration, avoiding Hobby's once-per-day restriction; Neon Function Triggers remain the target scheduler and must be provisioned.
- Node version pin 22.20.0.
- Dynamic Drizzle config using `DATABASE_URL_UNPOOLED` when available, otherwise `DATABASE_URL`; no hardcoded localhost credentials.
- Explicit database bootstrap SQL/script; no schema/role/RLS DDL on first request.
- Bounded PostgreSQL pool and production Neon-host validation.
- Health endpoint reports degraded auth/storage status rather than implying full readiness.
- Worker endpoint protected by constant-time Bearer secret comparison and fails closed if no secret exists.
- JSON and raw multipart bodies are read through streaming byte caps (2.5 MB JSON, 4 MB upload request); workspace rate limit reduced to 100/minute with one-second bucket sliding-window counting.
- Development OTP is emitted only in `X-Dev-OTP` / `X-Dev-OTP-Note` response headers, never in the JSON body; production local-auth is fail-closed.
- E2E test runner no longer falls back to localhost PostgreSQL; it requires a local app, Neon URL, and an explicit disposable-branch confirmation flag.
- Local CRON_SECRET is generated with cryptographically secure randomness and restrictive file permissions; value is not printed.
- Local auth is not accepted in production; OTP dev fallback remains development-only.
- Project Claude Code contract, internal skills/agents and bootstrap helper.

## Checks actually completed in this preparation environment
- ZIP extraction and file-tree inspection.
- JSON parse for `package.json` and `vercel.json`.
- Shell and Node script syntax checks.
- TypeScript/TSX syntax transpilation for all 54 source files: 0 syntax diagnostics (this is not a typecheck).
- Root-layout preflight script.
- JSON parsing of package.json, vercel.json and build state files.
- Secret-file scan: no `.env`, `.env.local`, private key, or keystore present in the prepared tree.
- Full npm install/build/test could not be run because the environment could not resolve `registry.npmjs.org` (DNS `EAI_AGAIN`).

## Required before commercial production
1. Connect the actual existing Neon project using owner-approved Neon OAuth; verify project and branch before provisioning.
2. Integrate Neon Managed Better Auth into the existing user/workspace model (including external auth subject mapping); verify OTP/email and session revocation.
3. Provision private Neon Object Storage bucket and integrate upload/download with compensation and signed URLs.
4. Deploy Neon Functions and Function Triggers; verify queue recovery, retries and trigger identity. `CRON_SECRET` must be generated and stored automatically if the worker endpoint is used.
5. Establish migration baseline on the target branch. Do not push the fresh schema blindly to an existing populated DB.
6. Run explicit schema migrations and database bootstrap as the correct owner role; verify `bf_runtime` membership and RLS as the runtime role.
7. Re-run typecheck, lint, build, 107 historical tests plus expanded tenant/accounting/concurrency/storage/auth tests on a dedicated Neon test branch.
8. Complete Android Capacitor build/device tests if Android is in release scope.
9. Complete vulnerability/SAST/DAST, accessibility WCAG 2.2 AA, performance/load, backup/restore, and legal/privacy review.
10. Deploy manually to `nbghyouth-ops/business-flow12` with Vercel Root Directory `./` or a commercial host. Verify the repository Vercel builds is the locked repo, not `business-flow144`.

No credentials or environment values are included in this archive.
