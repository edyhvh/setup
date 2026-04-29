---
name: cloudflare-deployment
description: Detailed standard Cloudflare deployment runbook for serverless projects. Use when deploying or migrating a project to Cloudflare Pages, Workers, D1, KV, and R2 with step-by-step setup, verification, rollback, and CI guidance.
license: MIT
---

# Cloudflare Deployment

## Overview

This skill is a reusable, project-agnostic deployment runbook for Cloudflare.
It is optimized for the most common serverless setup:

- Frontend on Cloudflare Pages
- Backend API on Cloudflare Workers
- SQL data in D1 when needed
- Cache and config in KV when needed
- File storage in R2 when needed

Use this when the goal is not just to "get something live", but to deploy it in a way that is repeatable, verifiable, and safe to hand off.

## When to Use

Use this skill when:

- A project is moving to Cloudflare for the first time
- A team needs a step-by-step deploy checklist with minimal ambiguity
- The frontend and backend will be deployed separately
- The backend needs Cloudflare bindings such as D1, KV, or R2
- You want a standard production/staging workflow instead of ad hoc commands
- You need a reliable release, verification, and rollback path

## Standard Deployment Pattern

Default to this unless the project clearly needs something else:

- Pages for the browser app or static site
- Workers for API routes, webhooks, auth, and server-side logic
- D1 for relational data close to the Worker
- KV for read-heavy config, flags, session-like lightweight data, and caches
- R2 for uploads, exports, media, and large objects

## Decision Table

| Project shape             | Default Cloudflare target |
| ------------------------- | ------------------------- |
| Static site only          | Pages                     |
| SPA with external API     | Pages                     |
| API only                  | Workers                   |
| Full-stack app            | Pages + Workers           |
| Relational data needed    | Add D1                    |
| Read-heavy key/value data | Add KV                    |
| File/object storage       | Add R2                    |

## Naming Convention Before You Start

Do this first. Most deployment confusion starts with inconsistent naming.

- Pick one short system name, for example `myapp`
- Define environments explicitly: `dev`, `staging`, `prod`
- Name resources with the environment suffix

Examples:

- Worker: `myapp-api`, `myapp-api-staging`
- Pages project: `myapp-web`
- D1: `myapp-prod-db`, `myapp-staging-db`
- KV binding: `CACHE`
- R2 bucket: `myapp-prod-files`
- Secrets: `JWT_SECRET`, `STRIPE_SECRET_KEY`, `TELEGRAM_BOT_TOKEN`

## Preflight Checklist

Do not deploy before all of this is known:

1. Which repo and branch are production?
2. What is the frontend build command?
3. What is the frontend output directory?
4. What is the Worker entrypoint file?
5. Which environment variables are public and which are secrets?
6. Which resources are required: D1, KV, R2, Queues, Durable Objects, none?
7. Which domains will point to Pages and which to Workers?
8. Is there a staging environment, or only production?

If any answer is unclear, stop and resolve it before deployment.

## Phase 1: Cloudflare Account and Access

### 1. Create or confirm the Cloudflare account

- Confirm the correct account is being used
- Confirm billing is enabled if the project needs paid features
- Confirm access to Workers & Pages, D1, KV, and R2 in the dashboard
- Confirm the custom domain, if any, is in the same Cloudflare account or that DNS delegation is already planned

### 2. Confirm team permissions

Before using CI or shared deployment scripts, verify:

- The deployment owner can create Workers and Pages projects
- The deployment owner can create D1, KV, and R2 resources
- The deployment owner can add domains, routes, and secrets
- CI will use a scoped API token, not a personal global token

## Phase 2: Local Tooling Setup

Cloudflare recommends installing Wrangler locally in the project.

### 1. Add Wrangler to the project

```bash
bun add -D wrangler@latest
```

### 2. Verify the CLI

```bash
bunx wrangler --version
```

### 3. Authenticate locally

Interactive login:

```bash
bunx wrangler login
```

Confirm the active account:

```bash
bunx wrangler whoami
```

Use API tokens for CI. Prefer not to depend on interactive login outside local development.

## Phase 3: Choose the Exact Deployment Topology

Pick one of these patterns and stay consistent.

### Pattern A: Frontend only

- Deploy only to Pages
- Use Git integration for automatic previews and production deploys

### Pattern B: API only

- Deploy only to Workers
- Optionally bind D1, KV, and R2

### Pattern C: Standard full stack

- Frontend on Pages
- API on Workers
- Frontend calls the Worker through a stable API base URL

This is the default pattern for most projects.

## Phase 4: Prepare the Worker Application

Use this phase for any backend, webhook receiver, auth service, or server-side function.

### 1. Confirm the Worker entrypoint

You need all of the following:

- A Worker entry file such as `src/index.ts`
- A local Wrangler config file: `wrangler.toml` or `wrangler.jsonc`
- A production-safe `compatibility_date`

### 2. Create or normalize the Wrangler config

Use a clear base config with explicit bindings.

Example `wrangler.toml`:

```toml
name = "myapp-api"
main = "src/index.ts"
compatibility_date = "2026-04-29"
workers_dev = true

[vars]
APP_ENV = "production"

[[d1_databases]]
binding = "DB"
database_name = "myapp-prod-db"
database_id = "REPLACE_WITH_REAL_ID"

[[kv_namespaces]]
binding = "CACHE"
id = "REPLACE_WITH_REAL_ID"

[[r2_buckets]]
binding = "FILES"
bucket_name = "myapp-prod-files"

[env.staging]
name = "myapp-api-staging"

[env.staging.vars]
APP_ENV = "staging"

[[env.staging.d1_databases]]
binding = "DB"
database_name = "myapp-staging-db"
database_id = "REPLACE_WITH_REAL_ID"

[[env.staging.kv_namespaces]]
binding = "CACHE"
id = "REPLACE_WITH_REAL_ID"

[[env.staging.r2_buckets]]
binding = "FILES"
bucket_name = "myapp-staging-files"
```

Rules:

- Never hardcode secrets in `wrangler.toml`
- Treat `vars` as non-secret configuration
- Use bindings for platform resources
- Keep production and staging resources separate

### 3. Create the D1 database if needed

Create production first:

```bash
bunx wrangler d1 create myapp-prod-db
```

Create staging separately:

```bash
bunx wrangler d1 create myapp-staging-db
```

Copy the returned `database_id` values into the correct binding blocks.

### 4. Create a KV namespace if needed

```bash
bunx wrangler kv namespace create CACHE
```

For staging, create a separate namespace and record its ID in `env.staging`.

### 5. Create an R2 bucket if needed

```bash
bunx wrangler r2 bucket create myapp-prod-files
```

And separately for staging:

```bash
bunx wrangler r2 bucket create myapp-staging-files
```

### 6. Add secrets before deployment

Examples:

```bash
bunx wrangler secret put JWT_SECRET
bunx wrangler secret put STRIPE_SECRET_KEY
bunx wrangler secret put TELEGRAM_BOT_TOKEN
```

For staging:

```bash
bunx wrangler secret put JWT_SECRET --env staging
```

Rules:

- Secret names should be consistent across environments
- Secret values should differ by environment when appropriate
- Never commit `.env` files that contain production secrets

## Phase 5: Database and Storage Initialization

This is the phase teams often skip, and it is a common reason a deployment appears successful but fails at runtime.

### 1. Create migration files or schema files

At minimum, have:

- schema definition
- initial indexes
- any required seed data for production boot

If the project has no migration tool yet, start with SQL files that are reviewed and committed.

### 2. Validate the schema locally first

```bash
bunx wrangler d1 execute myapp-prod-db --local --file=./migrations/0001_init.sql
```

Check a read query locally:

```bash
bunx wrangler d1 execute myapp-prod-db --local --command="SELECT name FROM sqlite_master WHERE type='table';"
```

### 3. Apply the schema remotely before the Worker goes live

```bash
bunx wrangler d1 execute myapp-prod-db --remote --file=./migrations/0001_init.sql
```

Verify remote data:

```bash
bunx wrangler d1 execute myapp-prod-db --remote --command="SELECT name FROM sqlite_master WHERE type='table';"
```

Do not deploy production code that expects tables or columns that do not yet exist remotely.

### 4. Seed KV or R2 only if the app depends on initial data

Example KV write:

```bash
bunx wrangler kv key put --binding=CACHE "healthcheck" "ok" --remote
```

Only seed R2 if boot assets, templates, or files are required at runtime.

## Phase 6: Local Verification Before First Deploy

Do not let the first production request be the first real test.

### 1. Run the Worker locally

```bash
bunx wrangler dev
```

### 2. Verify all critical paths locally

At minimum verify:

- health route returns `200`
- auth or session boot route works
- any D1 query path works
- any KV read/write path works if used
- any R2 upload/download path works if used
- CORS behavior is correct if frontend and API are separate origins

### 3. Decide the public API base URL now

Common choices:

- `https://myapp-api.<subdomain>.workers.dev`
- `https://api.example.com`

Prefer a custom domain for production-facing apps.

## Phase 7: Deploy the Worker

### 1. Deploy production

```bash
bunx wrangler deploy
```

For staging:

```bash
bunx wrangler deploy --env staging
```

### 2. Record the deployment output

Capture:

- deployed Worker name
- environment used
- workers.dev URL
- version ID if shown
- bound resources listed by Wrangler

If the output does not show the expected bindings, stop and fix config before proceeding.

### 3. Verify the live Worker immediately

Check:

- root or health endpoint
- one database-backed endpoint
- one secret-dependent endpoint if relevant
- logs or observability for runtime exceptions

## Phase 8: Deploy the Frontend to Pages

Git-connected Pages is the standard default because it gives preview deployments automatically.

### 1. Push the repository to GitHub

Cloudflare Pages expects a Git repository for the standard workflow.

Confirm:

- production branch exists, usually `main`
- build passes locally before connecting to Pages
- the frontend lives at a known root directory if the repo is a monorepo

### 2. Create the Pages project in the dashboard

Use this standard dashboard flow:

1. Open Workers & Pages
2. Select Create application
3. Select Pages
4. Select Import an existing Git repository
5. Choose the correct repository
6. Set the production branch
7. Set the root directory if the frontend is not at repo root
8. Set the build command
9. Set the build output directory

Typical examples:

- Build command: `bun run build`
- Output directory: `dist`
- Framework root directory: `apps/web`

### 3. Configure frontend environment variables and secrets

Before the first production build, set:

- public API base URL
- public analytics IDs if needed
- any build-time feature flags
- secrets only if the Pages project uses Pages Functions or server-side logic

Critical rule:

- If the frontend is built with a public env prefix convention, do not put secrets in variables that are bundled into client code.

### 4. Run the first Pages deploy

After setup, start the deploy from the dashboard. For later changes, Git pushes to the configured branch will redeploy automatically.

### 5. Verify the Pages output

Check:

- root page loads
- client-side routes work after refresh if applicable
- frontend points to the correct API
- no mixed-content or CORS errors occur
- preview deploys work on non-production branches if enabled

## Phase 9: Attach Custom Domains

This step is often treated as optional, but for production it should be part of the normal deploy process.

### 1. Pages domain

Attach the public web domain to the Pages project, for example:

- `example.com`
- `www.example.com`

### 2. Worker API domain

Attach the API domain to the Worker, for example:

- `api.example.com`

### 3. Re-test after domain attachment

Always re-test after custom domains are connected because:

- SSL issuance may still be propagating
- route or origin assumptions may differ from `workers.dev` and `pages.dev`
- frontend CORS allow-lists may need the production domain added

## Phase 10: Production Verification Checklist

Run this checklist immediately after deployment.

### Backend

- health endpoint returns `200`
- at least one read path works
- at least one write path works if the app supports writes
- secrets are available at runtime
- bindings are available at runtime
- no unexpected exceptions appear in logs

### Frontend

- production domain resolves correctly
- first page load succeeds
- JS assets load without `404`
- API calls hit the intended backend
- authentication flow works
- forms, uploads, and webhooks work if used

### Data

- production D1 schema is present
- required seed data exists
- KV keys exist if the app expects them
- R2 bucket access works if uploads/downloads are part of the flow

## Phase 11: CI/CD Standard

Use this baseline setup:

- Pages production deploys from Git integration on `main`
- Pages preview deploys from pull requests or non-production branches
- Worker deploys from CI with Cloudflare API token and account ID
- Staging deploys from a staging branch or workflow dispatch

Minimum CI secrets:

- `CLOUDFLARE_API_TOKEN`
- `CLOUDFLARE_ACCOUNT_ID`

Minimum CI steps for Workers:

1. Install dependencies
2. Build or typecheck if the project requires it
3. Run tests relevant to the Worker
4. Apply migrations if the release includes schema changes
5. Deploy with `bunx wrangler deploy`
6. Run a post-deploy health check

## Phase 12: Rollback Standard

Define rollback before the first production deployment.

### Pages rollback

- Redeploy the last known-good commit
- Or promote the last known-good Pages deployment from the dashboard

### Worker rollback

- Roll back to the previous stable Worker version from Cloudflare deployment history
- If schema changes are incompatible, do not roll back code without confirming database compatibility first

### Data rollback

- D1 migrations must be reversible or explicitly marked one-way
- Never assume code rollback automatically restores database state
- If data migrations are destructive, create backups or exports before applying them

## Common Failure Patterns

### Deploy succeeded but requests fail at runtime

Usually one of these:

- missing secret
- missing binding
- wrong environment deployed
- remote D1 schema not applied
- frontend points to staging API or old API URL

### Pages deploy succeeded but routes 404 on refresh

Check the framework output mode and SPA fallback behavior.

### Worker works on `workers.dev` but fails on custom domain

Check:

- route or custom domain mapping
- CORS allow-list
- auth callback URLs
- frontend env vars still pointing to the old host

### D1 works locally but not in production

Usually the remote schema was never applied. Local `--local` execution is not enough.

### KV looks empty in local dev

Wrangler local development uses local state by default unless remote bindings are explicitly configured.

## Minimum Definition of Done

The deployment is not complete until all of the following are true:

1. Frontend is live on the intended production domain or `pages.dev` URL
2. Worker is live on the intended production domain or `workers.dev` URL
3. Secrets are configured in the correct environment
4. D1, KV, and R2 bindings are present and tested if used
5. Remote database migrations are applied
6. End-to-end smoke tests pass
7. A rollback path is documented
8. A second person can repeat the deploy without guessing missing steps

## Recommended Prompt Behavior When Using This Skill

When applying this skill to a real project:

1. First classify the project as Pages-only, Workers-only, or full-stack
2. Inventory existing config files, build commands, env vars, and resource needs
3. Produce the exact deployment order for that repo
4. Separate public vars from secrets
5. Require a post-deploy verification checklist
6. Call out any missing prerequisite instead of guessing

## References

- https://developers.cloudflare.com/workers/wrangler/install-and-update/
- https://developers.cloudflare.com/workers/wrangler/commands/
- https://developers.cloudflare.com/pages/framework-guides/deploy-anything/
- https://developers.cloudflare.com/d1/get-started/
- https://developers.cloudflare.com/kv/get-started/
- https://developers.cloudflare.com/r2/get-started/
