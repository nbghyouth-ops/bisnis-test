# Project changelog — 2026-11-10

## Source base and references
- Newest uploaded `business-flow-platform-development(1).zip` is the source base.
- Previous combined/Vercel ZIPs and the prior Business Flow specification were used as references, not blindly overlaid.
- Preserved the existing root-flat Next.js source, business modules, 16 templates and existing DB-backed workflows.
- Target GitHub remains `nbghyouth-ops/business-flow12`; no new repository has been created or modified.

## Code and deployment hardening
- Added `.nvmrc` (Node 22.20.0), `vercel.json` and root preflight script. No Vercel Hobby Cron is registered.
- Replaced hardcoded localhost `drizzle.config.json` with env-based `drizzle.config.ts`.
- Moved database role/RLS/trigger bootstrap out of request lifecycle to `database/bootstrap.sql` + `scripts/db-bootstrap.mjs`.
- Added bounded PostgreSQL pool settings and production Neon-host check.
- Production local-auth paths now fail closed until Neon Managed Better Auth has been implemented; local OTP remains only for development.
- Worker tick requires `CRON_SECRET` and uses constant-time token comparison.
- JSON body reading uses a streaming byte limit; workspace rate limit changed to 100/minute.
- Health endpoint no longer runs privileged DDL and reports degraded provider readiness.
- Added Claude Code local skills/agents and safe Neon bootstrap helper.
- Updated README, deployment, readiness, blocker and release evidence docs.

## Not completed / not claimed
- Neon Auth SDK integration and user-subject mapping.
- Neon Object Storage integration.
- Neon Functions and scheduled Function Trigger deployment.
- Google OAuth, Android Capacitor app, XLSX/PDF, purchase returns, break-glass admin.
- Full npm install/typecheck/lint/build/E2E/security/accessibility/load/restore validation.
- Vercel deployment and production smoke test.

The environment could not resolve npm registry (`EAI_AGAIN`), so no package lock was generated and a full build could not be executed. This archive is a hardened deployment candidate, not a production-ready certification.


## Additional release-safety changes
- Changed PostgreSQL rate limiting to a rolling window counted in one-second buckets rather than a reset-at-boundary fixed window.
- Reasserted `bf_runtime` role attributes (`NOLOGIN`, `NOSUPERUSER`, `NOCREATEDB`, `NOCREATEROLE`, `NOBYPASSRLS`) during explicit database bootstrap.
- Scoped admin-health job/file/import aggregates to the active tenant via the RLS transaction; session count is limited to the authenticated user.
- Removed a sale-created queue item whose handler was a no-op, preventing a background job from being marked successful without performing work.
- Added `scripts/setup.sh` and `scripts/setup-neon.sh`; setup does not automatically migrate production.
- Drizzle CLI/bootstrap tooling now loads `.env.local` first, then `.env`, preserving the one-manual-input DATABASE_URL policy.

- Moved development OTP from JSON responses to development-only `X-Dev-OTP` headers; browser API client reads the header without exposing it in the response body.
- Removed the E2E suite's localhost PostgreSQL fallback; the suite requires a local app, a Neon-hosted test database URL and an explicit disposable-branch confirmation flag.
- Applied a streaming request-byte cap to raw multipart uploads as well as JSON requests; file-upload content is rewrapped only after the cap passes.
- Added local CRON_SECRET generation with cryptographic randomness and restrictive file permissions; the secret value is never printed.
- Added an expiry index and opportunistic stale-bucket cleanup for PostgreSQL-backed rate-limit rows.
