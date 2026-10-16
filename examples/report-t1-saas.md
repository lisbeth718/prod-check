# Ship check report: Lumen Invoicing

Fictional sample. The app, people and numbers are invented. This is an excerpt: 25 rows. A real quick run lists every in-scope row.

- App: Lumen Invoicing (invoicing SaaS, repo `lumen/app`)
- Tier: T1
- Gates on: accounts, multi-tenant, payments, transactional email, background jobs, API/webhooks
- Gates off: uploads, AI/LLM, multiple locales, health data, card data
- Mode: quick (all sections)
- Stack file used: none, adapted (Node API, managed Postgres, hosted queue)
- Date: 2026-03-12
- Auditor: audit agent A
- Reviewer: separate reviewer agent B

## Findings

| Section | ID | Check | Tier | Status | Evidence | Fix | Effort |
|---|---|---|---|---|---|---|---|
| Authorization | AUTHZ-01 | No IDOR | T1 | FAIL | `src/routes/invoices.ts:88` loads invoice by `req.params.id` with no owner check; replay as user B returned user A's invoice (200, 1.2 KB) in staging | Add tenant check in `getInvoice` and test | S |
| Authorization | AUTHZ-03 | Tenant scoping in query layer | T1 | FAIL | `src/db/reports.ts:41` raw SQL `SELECT ... FROM invoices` with no `tenant_id`; no RLS (`pg_policies` empty) | Enable RLS on tenant tables; use scoped helper | M |
| Backups | BKP-03 | Restore performed | T0 | FAIL | No drill log; owner says "never restored" | Restore last night's backup to scratch, verify row counts, schedule monthly | M |
| Backups | BKP-04 | Every app on the instance has backup and drill | T0 | FAIL | `\l` shows 3 databases; backup job covers `lumen` only | Add the other two to the backup job and drill | S |
| Billing | BILL-01 | Incoming webhook signatures verified | T1 | FAIL | `src/webhooks/payments.ts:19` parses body and trusts `event.type`; unsigned POST created a credit in staging | Verify signature header before parsing | S |
| Billing | BILL-02 | Idempotency keys on every charge | T1 | PASS | `src/billing/charge.ts:54` passes `idempotencyKey: invoice.id + attempt` | | |
| Database | DB-08 | Money as integer minor units | T1 | PASS | `invoices.total_cents bigint` (`migrations/004_invoices.sql:7`); no float math in `src/billing` (grep clean) | | |
| Database | DB-03 | Migrations reversible | T1 | FAIL | 6 of 14 migrations have no down step (`migrations/009` to `014`) | Write down steps; test up/down/up | M |
| Database | DB-17 | Database major version supported | T0 | PASS | `SHOW server_version;` = 16.4; supported until 2028-11 | | |
| Secrets | SEC-01 | Secret scan clean | T0 | FAIL | `gitleaks detect --log-opts=--all`: 1 finding, mail provider key in commit `a1b2c3d` (2025-11) | Rotate the key, then purge history | S |
| Secrets | SEC-02 | No secrets in public vars | T0 | PASS | `grep NEXT_PUBLIC_` equivalent: only `PUBLIC_API_URL`; bundle grep clean | | |
| Secrets | SEC-05 | Packages exist | T0 | PASS | 47 declared; all found in the registry with repos; `depcheck` lists 2 unused (flagged, not failed) | | |
| Authentication | AUTH-01 | Email normalization | T1 | PASS | Test `jo+billing@Example.com` signs up and logs in (`test/auth.test.ts:30`) | | |
| Authentication | AUTH-02 | Reset tokens hashed, single-use, expiring | T1 | PASS | `src/auth/reset.ts:22` stores sha256; reuse returned 400 in staging; expiry 30 min | | |
| Authentication | AUTH-05 | Login rate limit on real IP | T1 | UNKNOWN | Limiter exists (`src/auth/limit.ts:9`) but proxy header config not visible; no staging access to the edge | Confirm forwarded header handling at the edge | S |
| Input | WEB-02 | Parameterized queries only | T1 | FAIL | `src/db/reports.ts:41` builds SQL with a template literal containing `req.query.sort` | Whitelist sort columns; use parameters | S |
| Input | WEB-03 | No XSS | T1 | PASS | 1 raw HTML sink at `src/ui/Note.tsx:12`, input sanitized with DOMPurify | | |
| Deploys | DEP-03 | Tests run before production deploy | T1 | PASS | `.ci/deploy.yml:14` test job is a dependency of deploy; red test blocked a run on 2026-03-02 | | |
| Deploys | DEP-04 | Rollback tested | T1 | ATTESTED | owner says: "rollback is a button, used in January" | | |
| Observability | OBS-01 | External uptime checks | T0 | PASS | Monitor from two external regions hitting `/health`, alerting to the on-call channel | | |
| Observability | OBS-02 | Error tracking groups errors | T0 | PASS | Tracker DSN in production env; sample issue grouped 212 events | | |
| Observability | OBS-03 | Cost alerts | T0 | FAIL | Cloud budget alert exists; hosting provider has none | Add a usage alert to the hosting account | S |
| Email | MAIL-02 | DKIM, aligned SPF, DMARC | T0 | PASS | DKIM verified; return-path domain verified; `_dmarc` is `p=quarantine` | | |
| Email | MAIL-03 | Bounce and complaint handling | T0 | UNKNOWN | Events are routed to a queue, but the consumer code is in a repo the auditor cannot read | Provide access or a code excerpt | S |
| Operations | OPS-12 | Accounts owned by the company | T0 | ATTESTED | owner says: "domain is in my personal registrar account; cloud root is the company's, MFA on" | Move domain to the company account | S |

## Top 5 fixes

1. AUTHZ-01: add the owner check to `getInvoice` and every invoice route (S)
2. AUTHZ-03: scope or RLS every tenant table; fix `reports.ts` (M)
3. SEC-01: rotate the leaked mail provider key, then purge history (S)
4. BILL-01: verify the webhook signature before parsing (S)
5. BKP-03 and BKP-04: restore drill for all three databases (M)

## Re-verified by reviewer

AUTHZ-01, AUTHZ-03, BKP-03, BKP-04, BILL-01, DB-03, SEC-01, OBS-03, WEB-02
