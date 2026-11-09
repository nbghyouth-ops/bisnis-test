# Business Flow

Business Flow is a multi-tenant business-management platform built on Next.js 16 App Router, TypeScript, Drizzle ORM, and PostgreSQL. The repository is intentionally **root-flat**: `package.json`, `vercel.json`, and `src/app` are directly at the repository root so Vercel can detect the Next.js application when Root Directory is `./`.

## Locked project rules

- The only database is **Neon PostgreSQL**. Production rejects a non-Neon `DATABASE_URL`.
- The only environment value the owner should need to type manually is `DATABASE_URL`; Neon CLI should provision/pull Neon-managed variables and generated secrets. OAuth and owner approvals still require the owner once.
- No fake data, fake success responses, second database, payment gateway, wallet, escrow, or digital-money processing.
- Tenant reads/writes must run through `withTenant()` and PostgreSQL RLS with the non-BYPASSRLS role `bf_runtime`.
- Money uses PostgreSQL `NUMERIC` and `decimal.js`; posted journals, inventory movements, and audit history are immutable/corrected by reversal.
- Do not run production schema changes automatically at application startup. Generate, review, snapshot, migrate, and then run explicit database hardening.
- Local scrypt/OTP authentication is development-only. Production auth is intentionally fail-closed until Neon Managed Better Auth is provisioned and integrated. A code shown in a browser is not email verification.
- Neon Object Storage and Neon Functions/Function Triggers are the production targets. The current source still has a bounded PostgreSQL file fallback and database-backed job queue; the managed services must be provisioned and verified before client production.
- Vercel Hobby Cron is deliberately not configured. Use Neon Function Triggers for worker schedules or an eligible commercial host/scheduler. Vercel Hobby is not approved for commercial client use under Vercel terms.

## Requirements

- Node.js 22.20+
- npm 10.8+
- A Neon PostgreSQL project (production only)
- A separate Neon development/test branch for tests; do not point tests at customer production data

## Local setup

```bash
npm install
cp .env.example .env.local
# Set DATABASE_URL only. Do not commit .env.local.
npm run preflight
npm run typecheck
npm run lint
npm run build
```

The setup helper generates a local `CRON_SECRET` with cryptographic randomness and restrictive file permissions; it never prints the value. It does not synchronize this secret to production until an approved scheduler/worker environment is selected.

The app does not create roles, alter RLS, or change schema on the first request. Database setup is explicit:

```bash
npm run db:generate
npm run db:migrate
npm run db:bootstrap
```

**Safety rule:** use a new/empty Neon development branch for the initial generated migration. Do not run generated first-time migrations against an existing customer/production database until Claude Code has compared the live schema, created a backup/snapshot, produced a reviewed non-destructive migration, and received owner approval. `db:bootstrap` requires a database role with sufficient privileges to create/grant `bf_runtime` and manage policies/triggers.

## Neon / Claude Code setup

Run Claude Code from this repository and ask it to follow `CLAUDE.md` and `.claude/skills/`. The helper below installs the official Neon CLI if needed and invokes Neon’s project-scoped agent setup:

```bash
npm run neon:setup
```

Neon CLI may open an OAuth login flow or report that authentication/project selection is still required. Approve that in the browser. `neon init` cannot invent access to the owner’s Neon account. It should link the existing project (not create a duplicate) and pull Neon-managed values. Inspect `.env.local` locally; never paste or commit secrets.

Neon Managed Better Auth, Google OAuth, Object Storage, Functions and Function Triggers are not declared complete until they have been provisioned, the SDK integration has been implemented against the current official docs, and staging acceptance tests pass. Google OAuth may require owner action in Google Cloud. Do not display Google login as active before that succeeds.

## Vercel import / root configuration

Connect the existing repository `nbghyouth-ops/business-flow12` (the project’s locked repository; do not switch to `business-flow144` accidentally). In Vercel set:

| Setting | Value |
|---|---|
| Framework Preset | Next.js |
| Root Directory | `./` |
| Build Command | `npm run build` |
| Install Command | `npm install` |
| Output Directory | Default |
| Node.js | 22.x |

The `src/app` folder and `package.json` must be at that exact root. Do not leave everything nested under another `Business-Flow/` directory. Use Vercel Preview only where the plan terms allow. Commercial client production requires a plan/host whose terms expressly allow commercial usage. Do not add a `*/5 * * * *` Vercel Cron to Hobby; this release registers no Vercel cron and expects Neon Function Triggers after provisioning.

## Verification commands

```bash
npm run preflight
npm run typecheck
npm run lint
npm run build
BASE_URL=http://localhost:3000 npm run test:e2e -- --confirm-disposable-neon-branch
```

Historical `107/107` E2E results in previous reports are not proof that this hardened revision passes. Re-run the suite against a dedicated Neon test branch and record new results.

## Honest readiness

This archive is a **deployment/recovery candidate**, not a claim of complete production readiness. See `BLOCKERS.md`, `docs/PRODUCTION_READINESS.md`, and `RELEASE_VERIFICATION.md`. The environment used to prepare this archive could not reach npm registry, so it was not possible to install dependencies or run a full Next.js build here. Do not send real client traffic until Neon Auth, storage, worker triggers, migration baseline, security checks, backup/restore drill, and the full test gates are complete.


## Automated local setup

Requirements: Node.js 22.20+ and npm 10.8+. From the repository root:

```bash
npm run setup:neon
npm run setup
```

On Windows PowerShell, use `scripts\setup-neon.ps1` then `scripts\setup.ps1`. The Neon setup script also installs curated official Vercel/React, Anthropic frontend/testing, and Microsoft Playwright CLI skills for Claude Code automatically. It does not install arbitrary third-party marketplaces.


`setup:neon` uses the official Neon CLI to authenticate through the owner-approved browser OAuth flow, install the supported Neon Claude Code tooling, link the existing project/branch, and pull Neon-managed variables into the ignored `.env.local`. It does not ask you to paste API keys. If the Neon account contains multiple projects and the CLI cannot safely identify the Business Flow project, explicitly link the existing intended project rather than creating a duplicate.

`setup` installs dependencies, checks the app root, runs typecheck/lint/build, and does **not** run a database migration or destructive DDL. Before database bootstrap, review `docs/DATABASE_BOOTSTRAP.md`, verify the linked Neon branch, and take the required backup/snapshot.

Never commit `.env.local`, `.env`, `.neon`, OAuth credentials, or generated secrets.
