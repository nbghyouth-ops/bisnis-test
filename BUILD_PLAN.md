# Business Flow — implementation and release plan

## Locked project rules
- Existing GitHub repository: `nbghyouth-ops/business-flow12`; preserve `.git`, history and production data.
- One manual environment input at most: Neon `DATABASE_URL`. Prefer `neon env pull`; Neon/GitHub/hosting OAuth still requires owner consent.
- Neon PostgreSQL is the only database. No demo transactions, fake provider status, or fake success responses.
- All tenant operations use the tenant transaction wrapper and restricted `bf_runtime` role; system-level access remains narrowly scoped.
- Money is PostgreSQL `NUMERIC` + `decimal.js`; posted journals and stock/audit ledgers are immutable and corrected by compensating records.
- Never run schema mutation automatically on web request or application startup. Back up, review, stage, then migrate.
- Vercel root is flat (`package.json`, `vercel.json`, `src/app` at repo root). Use Root Directory `./`.
- Do not register a five-minute Vercel Hobby Cron. Prefer Neon Function Triggers; commercial client production must use a commercially permitted host/plan.

## Execution phases (resumable and idempotent)
1. **Preflight:** inspect repository, Node/npm versions, root layout, package manager, secrets, existing DB context, Vercel repo connection and all legacy configs. Preserve `.git`; create a backup branch before source replacement.
2. **Tooling:** install official Neon CLI/Claude Code Neon plugin/skills/MCP, plus only verified framework, GitHub, testing and security tools needed. Pin dependencies and record toolchain. Never use unsafe permission bypass.
3. **Neon linking:** authenticate owner through browser OAuth, reuse the existing Neon project and select an isolated development/staging branch. Do not create a duplicate project or print connection strings.
4. **Schema and migration:** compare live schema to Drizzle schema, establish a reviewed baseline, generate migrations, run schema diff, snapshot and rehearse on staging. Explicitly run DB bootstrap only after authorization.
5. **Identity:** integrate Neon Managed Better Auth; map its immutable subject to the Business Flow user UUID; enforce email verification, sessions, revocation and account linking. Google OAuth only after owner-authorized provider setup.
6. **Tenant security:** verify RLS on every tenant table, FORCE RLS as appropriate, role flags, grants, `USING`/`WITH CHECK`, transaction-local workspace context, tenant-aware foreign keys, and deny cross-tenant reads/writes/deletes.
7. **Business domain:** verify accounting double-entry, immutable journal/reversal, NUMERIC calculations, FIFO/weighted-average stock, BOM/production, purchasing/partial receipt/returns, POS/refunds, cash controls, booking, AR/AP and document numbering under concurrency.
8. **Files and import/export:** provision private Neon Object Storage; implement upload intent, signed download URLs, MIME/signature/size/checksum checks, tenant ownership and orphan cleanup. Add XLSX/PDF only with bounded parsers and safe fallbacks.
9. **Background work:** retain PostgreSQL durable jobs/outbox with leases, retries, backoff, idempotency, checkpoints and dead-letter replay. Deploy a branch-scoped Neon Function + Function Trigger; verify invocation authentication. A secret-protected route alone is not a scheduler.
10. **Product UX:** preserve the 17 module groups, 16 business templates, six feature groups, Simple/Advanced modes, onboarding, contextual help, responsive layout and accessible states. No sample business data in production.
11. **QA and security:** unit/integration/E2E, RLS cross-tenant matrix, financial reconciliation, stock concurrency, auth/session, file authorization, worker retry/replay, import safety, WCAG 2.2 AA, SAST/SCA/secret scan, performance and recovery tests.
12. **Release:** Node 22.20+, reproducible lockfile, typecheck, lint, production build, staging smoke, restore drill, production migration approval, correct GitHub repo/root, commercial hosting plan, deployment smoke and post-deploy monitoring.

## Current state
This archive contains root/deployment and source hardening, project-local Claude Code skills/agents, and automated setup helpers. It is **not production-ready yet**. Read `BUILD_STATE.json`, `BLOCKERS.md`, `TEST_RESULTS.md` and `docs/PRODUCTION_READINESS.md`. No production DB migration or account provisioning has been performed in this environment.
