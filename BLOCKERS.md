# Business Flow — blocker ledger (current archive)

> Status is intentionally honest. This archive is not yet client-production ready. Do not treat previous `107/107` results as a test of this hardened revision.

| Area | Current source status | Required to close |
|---|---|---|
| Vercel root / Next.js detection | Root-flat project; `src/app` and `package.json` are directly at repo root; `vercel.json` added without Hobby cron | Connect `nbghyouth-ops/business-flow12`, set Root Directory `./`, verify build logs |
| Neon PostgreSQL | Source uses PostgreSQL/Drizzle; production URL validation requires Neon hostname | Owner OAuth to existing Neon project; select/create isolated dev branch; verify URL and privileges |
| Migrations | Hardcoded localhost drizzle config removed; env-based `drizzle.config.ts`; migration commands added | Generate/review baseline migration; never push blindly to populated production; run migration on staging first |
| Database hardening | Runtime DDL removed; explicit `database/bootstrap.sql` and `npm run db:bootstrap` added | Run using authorized database owner connection; verify `bf_runtime`, grants, FORCE RLS, triggers and cross-tenant denial |
| Neon Managed Better Auth | Not yet integrated into this source; local auth is now dev-only and fails closed in production | Provision Neon Auth, install/pin current SDK, map Neon auth subject to Business Flow UUID user, implement OTP/email/session, test lifecycle |
| Email OTP / reset | Current local dev path only; never acceptable as production email verification | Verify Neon Managed Auth email delivery and abuse/rate controls |
| Google login | Disabled/not claimed active | Owner-authorized Google OAuth client/provider config, restricted OpenID scopes, account-linking and Android tests |
| Neon Object Storage | Current fallback is PostgreSQL bytea, 3 MB/file and 200 MB/workspace | Provision private Neon bucket; S3 SDK/signing; upload intents, saga, tenant access, signed URLs, quota/orphan cleanup |
| File scanning/re-encoding | MIME/signature allowlist and streaming request cap exist; no antivirus or image re-encoding | Add image re-encoding/metadata stripping; scanner only if actually deployed; parser/ZIP-bomb tests |
| Background jobs | PostgreSQL durable queue exists; endpoint now requires `CRON_SECRET` | Deploy Neon Function + Function Trigger; verify idempotency, lease, retries, checkpoint, DLQ/replay and branch scope |
| Vercel Cron | Deliberately not registered to avoid Hobby's more-than-daily error | Use Neon Function Triggers; do not restore `*/5` Vercel Cron on Hobby |
| XLSX/PDF | CSV plus print CSS fallback only | Implement XLSX parser / PDF server pipeline and resource/ZIP safety tests |
| Purchase returns | Incomplete | Return document, stock reversal, supplier credit/AP, balanced journals, approval, UI and tests |
| Platform admin/break-glass | Incomplete | Separate platform identity/role, short-lived approval, strong audit, two-person break-glass policy and tests |
| Android / Capacitor 8 | Not included in current ZIP | Implement mobile shell/API/auth/secure storage, API 36, emulator and device builds/tests |
| CI / supply-chain security | Local project skills included; remote CI not yet proven | Add GitHub Actions with full-SHA-pinned actions, least privilege, SAST/SCA/secret scan after repo OAuth |
| Backup / restore | Documentation only; no drill evidence | Choose Neon plan/backup policy, set RPO/RTO, restore to isolated branch and verify integrity |
| Accessibility/performance | Basic UI checks documented, no formal evidence in this revision | WCAG 2.2 AA audit, axe/keyboard/screen reader, performance budgets and authorized load tests |
| Legal/privacy | Templates/docs exist; no legal review | UU PDP, PSE, cross-border processing, subprocessor/DPA, retention, DSAR and incident-response legal review |
| Commercial hosting | No production provider approved | Use Vercel Pro/Enterprise or another host whose terms allow commercial client use; Hobby is personal/non-commercial only |
| Full release verification | Not run here; npm registry DNS unavailable (`EAI_AGAIN`) | Install dependencies in user environment/CI; run typecheck, lint, build, tests, Neon smoke, security and recovery gates |

## Rules
- No data provider other than Neon PostgreSQL.
- No secrets committed, pasted into chat, or printed in output.
- No fake provider status or success status.
- No destructive schema operation without snapshot, reviewed plan and explicit owner approval.
- Production remains blocked while any critical/high defect or required provider integration is unverified.


## Deployment blockers after source hardening

- Neon Managed Better Auth is not yet integrated with the existing `users` / `workspace_members` model. Local scrypt/DB auth is deliberately disabled in production. No client registration/login should be accepted until the managed auth bridge and email-verification flow are implemented and tested.
- Neon Object Storage and signed upload/download are not yet integrated; only the bounded PostgreSQL bytea fallback exists.
- Neon Functions / Function Triggers are not deployed. The secret-protected worker endpoint is not itself a scheduler.
- Google OAuth is not active. Enable it only after the owner authorizes Google OAuth provider credentials through Neon.
- No CI workflow, full security scan, staging migration rehearsal, or restore drill has been verified in this environment.
- XLSX/PDF, purchase returns, platform admin/break-glass, and Capacitor Android remain incomplete according to the original project blocker list.
- Historical 107/107 E2E is not evidence for this changed revision; rerun the current suite against an isolated Neon test branch using `npm run test:e2e -- --confirm-disposable-neon-branch`. The suite no longer falls back to a local PostgreSQL database.
- A real Vercel build/deployment has not been verified. Use the existing `nbghyouth-ops/business-flow12` repository, ensure root-flat files, and set Vercel Root Directory to `./`.
