# Ship check report: Lumen Invoicing

Fictional sample. The app, people and numbers are invented. This is an excerpt: 29 rows. A real run lists every in-scope row.

- App: Lumen Invoicing (invoicing SaaS, repo `lumen/app`)
- Tier: T1 Paying
- Gates on: accounts, multi-tenant, payments, transactional email, background jobs, API/webhooks, database
- Gates off: uploads, imports, usage billing, URL fetching, impersonation, AI/LLM, multiple locales, health data, card data, self-hosted servers
- Mode: full (all sections)
- Stack file used: none, adapted (Node API, managed Postgres, hosted queue)
- Date: 2026-03-12
- Auditor: audit agent A
- Reviewer: separate reviewer agent B
- Access gaps: no staging access to the edge proxy; consumer repo for mail events not readable

## Findings

| Section | ID | Check | Tier | Stage | Status | Evidence | Fix | Effort |
|---|---|---|---|---|---|---|---|---|
| Secrets | SEC-01 | Secret scan clean | T0 | - | FAIL | `gitleaks git --log-opts=--all .`: 1 finding, mail provider key in commit `a1b2c3d` (2025-11) | Rotate the key, then purge history | S |
| Authorization | AUTHZ-01 | No IDOR | T1 | invoice | FAIL | `src/routes/invoices.ts:88` loads invoice by `req.params.id` with no owner check; replay as user B returned user A's invoice (200, 1.2 KB) in staging | Add tenant check in `getInvoice` and test | S |
| Authorization | AUTHZ-03 | Tenant scoping in query layer | T1 | invoice | FAIL | `src/db/reports.ts:41` raw SQL `SELECT ... FROM invoices` with no `tenant_id`; `relrowsecurity` is false on all tenant tables | Enable RLS on tenant tables; use scoped helper | M |
| Input | WEB-02 | Parameterized queries only | T1 | invoice | FAIL | `src/db/reports.ts:41` builds SQL with a template literal containing `req.query.sort` | Whitelist sort columns; use parameters | S |
| Backups | BKP-03 | Restore performed | T0 | - | FAIL | No drill log; owner admits "never restored" | Restore last night's backup to scratch, verify row counts, schedule monthly | M |
| Backups | BKP-04 | Every app on the instance has backup and drill | T0 | - | FAIL | `\l` shows 3 databases; backup job covers `lumen` only | Add the other two to the backup job and drill | S |
| Billing | BILL-01 | Incoming webhook signatures verified | T1 | invoice | FAIL | `src/webhooks/payments.ts:19` parses body and trusts `event.type`; unsigned POST created a credit in staging | Verify signature header before parsing | S |
| Billing | BILL-14 | Card-testing protection | T1 | invoice | FAIL | `src/routes/checkout.ts:31` creates a payment intent for any anonymous POST; no rate limit, no bot challenge, provider fraud rules at defaults | Rate-limit checkout creation per IP, add a bot challenge, turn on the provider's card-testing rules | S |
| Observability | OBS-03 | Cost alerts | T0 | - | FAIL | Cloud budget alert exists; hosting provider has none | Add a usage alert to the hosting account | S |
| Operations | OPS-12 | Accounts owned by the company | T0 | - | FAIL | Registrar record shows the domain under a personal account; owner admits it. Cloud root is the company's, MFA on | Move domain to the company account | S |
| Authentication | AUTH-05 | Login rate limit on real IP | T1 | invoice | UNKNOWN | Limiter exists (`src/auth/limit.ts:9`) but proxy header config not visible; no staging access to the edge | Confirm forwarded header handling at the edge | S |
| Email | MAIL-03 | Bounce and complaint handling | T0 | - | UNKNOWN | Events are routed to a queue, but the consumer code is in a repo the auditor cannot read | Provide access or a code excerpt | S |
| Secrets | SEC-03 | Pasted keys rotated | T0 | - | ATTESTED | owner says: "all keys from the January chat export were rotated on 2026-02-10"; no rotation log seen | | |
| Secrets | SEC-02 | No secrets in public vars | T0 | - | PASS | Only `PUBLIC_API_URL` is client-exposed; bundle grep clean | | |
| Secrets | SEC-05 | Packages exist | T0 | - | PASS | 47 declared; all found in the registry with repos; `depcheck` lists 2 unused (advisory note only) | | |
| Authorization | AUTHZ-07 | Browser-reachable data has row-level security or locked rules | T1 | invoice | PASS | Browser talks only to the API (`src/ui/api.ts`); no database URL or public data-store key in the bundle (grep clean); database port closed to the internet | | |
| Authentication | AUTH-01 | Email normalization | T1 | 10 | PASS | Test `jo+billing@Example.com` signs up and logs in (`test/auth.test.ts:30`) | | |
| Authentication | AUTH-02 | Reset tokens hashed, single-use, expiring | T1 | invoice | PASS | `src/auth/reset.ts:22` stores sha256; reuse returned 400 in staging; expiry 30 min | | |
| Database | DB-08 | Money as integer minor units | T1 | invoice | PASS | `invoices.total_cents bigint` (`migrations/004_invoices.sql:7`); no float math in `src/billing` (grep clean) | | |
| Database | DB-03 | Migrations versioned and apply in order | T1 | 10 | PASS | Migrations are versioned (`migrations/001` to `014`); all 14 ran in order from empty on a scratch DB |  |  |
| Database | DB-17 | Database major version supported | T0 | - | N/A | not applicable to this architecture: managed Postgres, provider enforces major-version upgrades | | |
| Input | WEB-03 | No XSS | T1 | invoice | PASS | 1 raw HTML sink at `src/ui/Note.tsx:12`, input sanitized with DOMPurify | | |
| Email | MAIL-02 | Sending access, DKIM, aligned SPF, DMARC, bounce rates | T0 | - | PASS | Production access enabled; DKIM verified; return-path domain verified; `_dmarc` is `p=quarantine`; 30-day bounce 0.8%, complaints 0.02% | | |
| Deploys | DEP-03 | Tests run before production deploy | T1 | 10 | PASS | `.ci/deploy.yml:14` test job is a dependency of deploy; red test blocked a run on 2026-03-02 | | |
| Deploys | DEP-04 | Rollback used | T0 | - | PASS | `deploy/rollback.sh` present; deploy log shows a rollback run on 2026-01-14 (`deploy-history.log:212`) | | |
| Observability | OBS-01 | External uptime checks | T0 | - | PASS | Monitor from two external regions hitting `/health`, alerting to the on-call channel | | |
| Observability | OBS-02 | Error tracking groups errors | T0 | invoice | PASS | Tracker DSN in production env; sample issue grouped 212 events | | |
| Billing | BILL-02 | Idempotency keys on every charge | T1 | invoice | PASS | `src/billing/charge.ts:54` passes `idempotencyKey: invoice.id + attempt` | | |
| Server | SRV-01 | Database not internet-reachable | T0 | - | N/A | not applicable to this architecture: managed Postgres, no self-hosted servers | | |

## Advisory notes

- DB-01 (advisory): `invoice_notes` has no foreign key to `invoices`; add one when next touching the table.
- CODE-02 (advisory): `src/db/reports.ts` is 2,300 lines; split by report type.

## Top 5 fixes

1. SEC-01: rotate the leaked mail provider key, then purge history (S)
2. AUTHZ-01: add the owner check to `getInvoice` and every invoice route (S)
3. AUTHZ-03: scope or RLS every tenant table; fix `reports.ts` (M)
4. WEB-02: whitelist sort columns and parameterize `reports.ts` (S)
5. BKP-03 and BKP-04: restore drill for all three databases (M)

## Re-verified by reviewer

Every FAIL: SEC-01, AUTHZ-01, AUTHZ-03, WEB-02, BKP-03, BKP-04, BILL-01, BILL-14, OBS-03, OPS-12.
PASS in scope of the rule: AUTHZ-07, AUTH-01, AUTH-02, WEB-03, BILL-02.
