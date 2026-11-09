# Business Flow — Non-negotiable Engineering Contract

## Product and data rules
- Preserve the existing Business Flow modules, 16 business templates, Simple/Advanced modes, and design system. Do not silently drop features.
- Neon PostgreSQL is the only database. Never add Supabase, Firebase, MySQL, MongoDB, SQLite, Redis-as-database, or a second source of truth.
- The only manually entered ENV value is `DATABASE_URL`. Provision/pull all other platform values and generate internal secrets automatically. Browser OAuth and owner approvals cannot be bypassed.
- No fake/demo records or fake success responses in production. Never claim a provider, integration, test, migration, or deployment passed unless evidence exists.
- No LLM/AI end-user feature, payment gateway, wallet, escrow, or digital-money processing.

## Database and tenant security
- PostgreSQL + Drizzle only. Use `NUMERIC` + `decimal.js` for every money amount; never JS floating point for money.
- Every tenant business operation must use `withTenant()` and execute in a single transaction with transaction-local workspace/user context. This is required for Neon PgBouncer transaction pooling.
- `bf_runtime` must be `NOLOGIN`, `NOSUPERUSER`, `NOBYPASSRLS`, and must not own tenant tables. Enable and FORCE RLS and apply both `USING` and `WITH CHECK` to all tenant-scoped tables.
- `withSystem()` is restricted to identity, workspace provisioning and trusted worker paths. Never expose it to user-controlled route handlers.
- Never run DDL, role creation, RLS changes, seed/test data, or destructive migrations on app startup or health checks. Use reviewed migration files and explicit bootstrap scripts.
- Journals, stock movements, cost snapshots, legal acceptances and audit logs are append-only. Correct posted facts with reversal/correction flows.
- Transactions must be atomic, idempotent, concurrency-safe, audited, and report success only after commit.

## Auth, files and jobs
- Local scrypt/DB sessions/OTP are development-only. Production must fail closed until Neon Managed Better Auth is actually provisioned and the official SDK integration is implemented/tested.
- Never send OTP in production response/body/header/log. A code displayed in a browser does not prove email ownership. In development only, if an existing local-auth flow needs a test code, return it only in `X-Dev-OTP` / `X-Dev-OTP-Note` response headers, never the JSON body.
- Google login must remain hidden/disabled until Google OAuth is provisioned and the server-side identity flow passes tests. Never request Gmail/Drive/Calendar scopes.
- Files are private by default, tenant-owned, signature/MIME/size/checksum validated, and downloaded with authorized short-lived URLs. No permanent Vercel local filesystem storage.
- Durable jobs live in PostgreSQL with lease, idempotency, retry/backoff, checkpoint, dead-letter, replay and tenant fairness. Use Neon Function Triggers for schedules; do not rely on Vercel Hobby cron.

## Quality and delivery
- Keep the repository root-flat: `package.json` and `src/app` at root. Existing target repository is `nbghyouth-ops/business-flow12`; preserve `.git` and create a backup branch before replacing source.
- Run preflight, typecheck, lint, build, unit/integration/E2E, security, accessibility and restore checks. Capture actual command output and update `RELEASE_VERIFICATION.md`.
- Never upload secrets. Never use `--dangerously-skip-permissions`. Never drop/truncate/overwrite a production DB or change a live schema without backup, reviewed plan and explicit owner approval.
- If any tool/skill/plugin cannot be installed or authenticated, use a safe equivalent or record a blocker; do not invent successful setup.
- Do not claim zero bugs or 100% reliability. Release gate is zero known critical/high defects, all defined acceptance tests passed, and operational recovery verified.
