# Test results — 2026-11-10

## Current revision
**Not run / not verified.** The local E2E harness now reads development OTP from the `X-Dev-OTP` header (not JSON), requires a local app URL and a Neon URL, and refuses to run unless `--confirm-disposable-neon-branch` is supplied. The current sandbox could not resolve `registry.npmjs.org` (`EAI_AGAIN`), so dependencies were not installed. Do not interpret the status as a pass.

## Historical test result
The prior source documentation reports **107/107 E2E tests passed** and some manual route/UI checks. This is historical evidence only and was not rerun after the current changes to database bootstrap, production auth gating, worker authorization, rate limiting and body streaming.

## Required test matrix
- Root preflight: `npm run preflight`
- TypeScript: `npm run typecheck`
- Lint: `npm run lint`
- Production build: `npm run build`
- E2E: `BASE_URL=http://localhost:3000 npm run test:e2e -- --confirm-disposable-neon-branch`
- Tenant isolation, RLS + pooled connections
- Accounting debit/credit, reversals and closed periods
- Stock FIFO/weighted average, concurrent stock and POS
- Auth lifecycle and Neon Managed Better Auth verification
- Private object upload/download and cross-tenant denial
- Queue idempotency, lease expiry, retry, DLQ and replay
- Accessibility, dependency/security scan and restore drill

Run database tests only on a dedicated Neon test branch, never on customer production.
