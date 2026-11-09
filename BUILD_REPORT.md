# Business Flow — source hardening report (2026-11-10)

## Release status
**NOT PRODUCTION READY.** This package is a root-flat deployment/recovery candidate with source hardening. It is not a certification that all Business Flow modules or managed integrations are complete.

## Work completed in this archive
- Preserved the uploaded Business Flow source and its App Router pages, components, API routes, schema and existing module workflows.
- Prepared the repository root for Vercel: `package.json`, `next.config.ts`, `vercel.json` and `src/app` are at the archive root.
- Added `vercel.json` without a Vercel Cron entry; the Vercel Hobby `*/5 * * * *` deployment error is not reintroduced.
- Removed the hardcoded localhost database URL from Drizzle configuration; DB CLI and app use Neon environment URLs.
- Moved role/RLS/trigger DDL out of request/health paths to an explicit reviewed bootstrap command.
- Reasserted restrictive `bf_runtime` role attributes and documented the RLS/migration rehearsal needed before production.
- Added small bounded PostgreSQL pooling, production Neon-host validation, JSON and multipart streaming body limits, tenant-scoped admin health queries, sliding-window rate limiting and stale rate-bucket cleanup.
- Production local scrypt/session/OTP auth fails closed until Neon Managed Better Auth is provisioned and bridged to the current Business Flow identity model.
- Development OTP is carried in `X-Dev-OTP` / `X-Dev-OTP-Note` response headers only; it is not included in the JSON body.
- Worker tick requires a constant-time-verified Bearer secret; a local 256-bit `CRON_SECRET` can be generated without printing it.
- E2E no longer falls back to a local PostgreSQL URL. It requires a local app, Neon database URL and explicit disposable-test-branch confirmation.
- Added Claude Code project skills/reviewer agents and setup helpers for Neon CLI, Neon plugin/skills/MCP, Vercel/React skills, Anthropic frontend/testing skills, and Playwright CLI skills.
- Added deployment, database bootstrap, acceptance, blocker, recovery and production-readiness documentation.

## Checks actually completed in this preparation environment
- Root layout preflight passed.
- TypeScript/TSX syntax-only transpilation: **54 files, zero syntax diagnostics**. This is not `tsc --noEmit`.
- Node `.mjs` syntax and Bash `.sh` syntax checks passed.
- `package.json`, `vercel.json`, `BUILD_STATE.json` and `TOOLCHAIN_LOCK.json` parsed as valid JSON.
- Verified no Vercel `crons` property, no localhost PostgreSQL fallback in runtime/test setup, and no secret/key files in this package tree.
- ZIP integrity and root layout are verified in the final archive.

## Checks NOT completed
- `npm install` / `npm ci` and lockfile generation.
- Full TypeScript typecheck, ESLint, Next.js production build.
- Current E2E suite and database-backed RLS/accounting/concurrency tests.
- Neon owner OAuth, Neon Managed Better Auth, email delivery, Object Storage, Neon Functions and Function Triggers.
- GitHub/Vercel connection, deployment, production smoke tests, Android build/device checks, backup/restore drill, accessibility/performance/security scans.

## Environment constraints
- The preparation environment could not resolve `registry.npmjs.org` (`EAI_AGAIN`), so packages could not be installed and no `package-lock.json` could be generated.
- Available Node is `v22.16.0`, below the project toolchain floor `22.20.0`; setup scripts correctly stop rather than pretending tool compatibility.
- Therefore `next build`, `tsc --noEmit`, `eslint`, E2E and live Neon tests were not run for this revision.
- Historical `107/107` E2E is preserved as historical evidence only, not a pass for this source revision.

## Production blockers
See `BLOCKERS.md` and `RELEASE_VERIFICATION.md`. The most important are Neon Managed Better Auth, private Neon Object Storage, real Neon Function/Trigger worker deployment, reviewed migration baseline/RLS validation, fresh typecheck/lint/build/E2E, production Vercel env sync, restore rehearsal, and legal/security review.
