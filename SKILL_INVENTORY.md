# Claude Code tool/skill inventory

This archive includes project-local skills and read-only reviewer agents under `.claude/`. They do not require manual marketplace installation.

| Source | Purpose | State |
|---|---|---|
| Project-local Business Flow skills | Neon provisioning, tenant security, accounting/inventory, release verification | Included |
| Project-local reviewer agents | Architecture, DB security, accounting, security | Included |
| Official Neon CLI + Neon plugin/skills/MCP | Neon provisioning and current service docs | `scripts/setup-neon.sh` invokes the official CLI login and non-interactive agent setup; owner browser approval may be required |
| Vercel official agent skills | React/Next.js performance, composition and Vercel deployment review | `scripts/setup-neon.sh` / `.ps1` installs curated skills from `vercel-labs/agent-skills` |
| Anthropic official skills | Frontend design and web app testing | `scripts/setup-neon.sh` / `.ps1` installs `frontend-design` and `webapp-testing` from `anthropics/skills` |
| Microsoft Playwright tooling | Browser automation and visual QA | `@playwright/cli` and its Claude Code skill are installed by `scripts/setup-neon.sh` / `.ps1`; full browser suite still needs test authoring/configuration |
| Local secret preparation | `scripts/prepare-local-secrets.mjs` generates a 256-bit worker secret without printing it; `.env.local` is ignored |
| GitHub CLI / GitHub MCP / Actions | Existing repo, CI and automation | OAuth and repository permission required; no tokens stored in archive |

Security policy: do not install random marketplaces or execute unreviewed install scripts. Plugins/skills are developer tooling, not website runtime. Use `neon init --yes --agent claude-code` for Neon’s official supported onboarding; this may require `neon auth` via the owner’s browser. Do not use `--dangerously-skip-permissions`.
