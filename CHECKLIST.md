# Prod check: checklist

Every check in one flat list, for humans. Tier: T0 = Public site (no accounts, no payments, no customer data); T1 = Paying (any of: accounts, customer data, payments); T2 = Enterprise or regulated.
Stage: `launch` = before the first real user outside the team or the first invoice, whichever comes first, `10` = before 10 customers, `100` = before 100 customers. T0 and T1 rows carry a stage; T2 and advisory rows have none. [A] = advisory: reported as a note, never a FAIL. Do everything at or below your tier. Skip a section when its gate (a feature you do not have) is off. `(attest)` means a person answers it; an auditor cannot verify it from code.
A `multi-tenant` gate means an org, team or workspace entity where several users share records; per-user ownership alone is not multi-tenant. T2 regulated data means health, financial account, legal, member, union or political data; ordinary PII (CVs, emails) stays T1.
How to verify each item, and what evidence to keep, is in `references/checks/`.

## Server and network

- [T0 launch] (gate: self-hosted servers, database) SRV-01 The database is not reachable from the internet.
- [T0 launch] (gate: self-hosted servers) SRV-02 If a CDN/WAF fronts the origin, the origin accepts web traffic only from it and CDN-to-origin TLS is fully verified. N/A when no CDN is used.
- [T0 launch] (gate: self-hosted servers) SRV-03 SSH is key-only, root password login is disabled, unattended security upgrades are on.
- [T0 launch] SRV-04 Encryption in transit everywhere, including app-to-database traffic between hosts.
- [T0 launch] SRV-06 Domains are on auto-renew with a valid payment method; certificate expiry is tracked with alerts before expiry, not on the day.
- [T1 100] SRV-07 A CDN caches static assets so the app loads fast far from the origin.
- [A] SRV-08 A second region or standby exists only if a contract promises uptime one box cannot deliver; autoscaling is needed only if load testing shows it.
- [T0 launch] (gate: self-hosted servers) SRV-09 Disk-fill protection: log rotation (journald and container log driver size caps), a scheduled image and build-cache prune, and a disk alert at about 80%.
- [T0 launch] (gate: self-hosted servers) SRV-10 Server timezone is UTC, the clock is NTP-synced, and containers have memory limits and a restart policy.
- [T1 launch] SRV-11 Production does not run on a plan that pauses, sleeps or has no backups (e.g. a free DB project that pauses when idle, a free web tier that spins down); the plan's backup/PITR retention is stated.

## Secrets and supply chain

- [T0 launch] SEC-01 Secret scan over full git history is clean and env files are not committed.
- [T0 launch] SEC-02 Secrets never appear in client-exposed environment variables or any client bundle.
- [T0 launch] SEC-03 Every key pasted into an agent chat, MCP config, or committed config file has been rotated. (attest)
- [T0 launch] SEC-04 No known critical or high vulnerability in a reachable production dependency; lockfile committed; framework major version still supported.
- [T0 launch] SEC-05 Every declared package exists in its registry with a real repository and plausible history. Unused packages are an advisory note, not a FAIL.
- [T0 launch] SEC-06 Least privilege for everyone, including agents: production DB URLs, SSH keys and cloud keys in agent or MCP configs are scoped to what the task needs.
- [T0 launch] SEC-08 Live and test credentials are separated per environment: no test-mode or sandbox keys in production, no live keys in dev or staging.
- [T1 launch] SEC-09 Server components and loaders do not pass whole database rows to client components or serialized props (password hashes, tokens, other users' data).
- [T0 launch] SEC-10 No debug, seed, test or admin-bootstrap routes, default or demo accounts, or hardcoded admin credentials are reachable in production.

## Authentication (gate: accounts)

- [T1 10] AUTH-01 Emails are normalized by lowercasing and trimming, the `+tag` is kept, and uniqueness is enforced by a unique index on `lower(email)` or a case-insensitive type; login works for `name+tag@domain`.
- [T1 launch] AUTH-02 Reset tokens are hashed in the DB, single-use and expiring.
- [T1 10] AUTH-03 MFA is available and enforced for admin accounts, with a recovery path for a lost device.
- [T1 10] AUTH-04 Sessions time out, all sessions for one user can be killed at once, and password change and reset invalidate other sessions.
- [T1 launch] AUTH-05 Login and reset are rate-limited on the real client IP, not the proxy's IP.
- [T2] AUTH-06 Lockouts slow attackers (progressive delay, per account and per IP) without hard-locking real users, and an admin unlock path exists.
- [T2] AUTH-07 SSO (SAML and OIDC) is supported for customers who require it.
- [T2] AUTH-08 SCIM provisioning and deprovisioning removes access immediately when IT offboards someone.
- [T1 launch] AUTH-09 Passwords are hashed with a password hash (argon2id, bcrypt or scrypt) at a sane cost.
- [T1 launch] AUTH-10 Session cookies set HttpOnly, Secure and SameSite=Lax or Strict.
- [T2] AUTH-11 Login, password reset and signup do not reveal whether an account exists: same response and similar timing for existing and missing accounts.
- [T1 launch] AUTH-12 Email is verified before any costly action (AI, email, SMS, uploads). Stage launch only when signup is public and AI, SMS or email sends are open to new accounts; otherwise stage 10.
- [T2] AUTH-13 MFA is available to all users, with a recovery path for a lost phone.
- [T1 launch] AUTH-14 OAuth account linking happens only on a verified email (no automatic link to an unverified address); changing email requires re-verification.
- [T1 10] AUTH-15 Signup abuse controls: bot challenge, disposable-email block and a per-IP signup limit.
- [T1 launch] AUTH-16 The server verifies token signatures (verify, not decode) and server code trusts only a verified user (e.g. Supabase getUser(), not getSession(), on the server).
- [T1 launch] AUTH-17 Auth emails (verify, reset, magic link) go through a production mail provider, not the auth vendor's rate-limited default SMTP.

## Authorization and tenancy (gate: accounts)

- [T1 launch] AUTHZ-01 Every server action and route handler checks the session and that the record belongs to that user or tenant (no IDOR).
- [T1 launch] AUTHZ-02 Roles and permissions are enforced on the server, in handlers or the data layer, not only by hiding UI.
- [T1 launch] (gate: multi-tenant) AUTHZ-03 Tenant scoping is enforced in the query layer or database row-level security, not only in the UI.
- [T1 100] (gate: impersonation exists) AUTHZ-04 Admin "log in as user" for support writes every use to an audit log.
- [T2] AUTHZ-05 An audit trail records who viewed a record, not only who changed it.
- [T2] AUTHZ-06 Quarterly access reviews are signed off by a named person. (attest)
- [T1 launch] AUTHZ-07 Every table reachable with a browser or anon key has row-level security or locked security rules, with a per-user test. Applies when a browser or mobile client can query the data store directly with a public key; a per-user app with RLS off exposes the whole database.
- [T1 launch] AUTHZ-08 Auth is enforced inside the page, route handler or server action, not only in middleware (middleware bypass is a known bug class, e.g. CVE-2025-29927).
- [T1 launch] AUTHZ-09 Update handlers whitelist writable fields; a user cannot set role, plan, is_admin, tenant_id/org_id, owner_id or price via the request body (no mass assignment).

## Database and data model

- [A] DB-01 The schema is normalized where it should be, with primary keys, constraints and foreign keys present.
- [A] DB-02 The data model survives the second customer's "one small change": settings or configurable fields instead of hardcoded single-customer logic.
- [T1 10] DB-03 Migrations are versioned and applied in order; forward-only is acceptable.
- [T1 100] DB-04 Migrations take no long table locks.
- [T1 100] DB-05 Indexes exist on foreign keys and filtered or sorted columns, checked against production-sized data.
- [T1 launch] DB-06 Connection pooling is in place with headroom under the DB connection limit across every app sharing the instance. Pooling is required when serverless or edge functions talk to Postgres, because they exhaust connections otherwise.
- [T2] DB-07 Pagination stays fast at depth (keyset or cursor, not large OFFSET).
- [T1 launch] DB-08 Money is stored as integer minor units; never as floating point.
- [T1 100] DB-10 Timestamps are stored as timezone-aware types; user-facing schedules are computed in the user's timezone.
- [T1 10] DB-13 GDPR deletion reaches every table, object storage, logs and the email suppression list where relevant, with a stated backup expiry.
- [T2] DB-14 Customers can export their data (CSV or XLSX).
- [T1 100] (gate: imports) DB-15 Imports survive real-world CSVs: BOMs, Windows-1252, semicolon delimiters, Excel date formats, quoted newlines.
- [T2] DB-16 Data residency matches contracts: hosting location, object storage jurisdiction, email region and model provider region.
- [T0 launch] (gate: database) DB-17 The database major version is still within upstream security support. N/A on managed databases where the provider enforces upgrades (state which).
- [T1 100] (gate: database) DB-18 The application's database role is not a superuser or the table owner, has no BYPASSRLS, and holds least-privilege grants.
- [T1 10] DB-19 Tables that grow without bound (sessions, logs, events, jobs, AI transcripts) have TTL or pruning, retention rules from contracts and the privacy policy run as scheduled deletion code, and database size is alerted.

## Backups and recovery (gate: database)

- [T0 launch] BKP-01 Nightly off-box backups exist and are encrypted, in a separate bucket and with credentials separate from the app. User-uploaded files are backed up or versioned too, not only the database.
- [T1 100] BKP-02 Continuous archiving enables point-in-time recovery.
- [T0 launch] BKP-03 A restore has actually been performed; the date of the last drill is recorded. (attest)
- [T0 launch] BKP-04 Every app sharing the same database instance has its own backup and restore drill, not just this app.
- [T2] BKP-05 A disaster recovery plan is written down and tested at least yearly. (attest)

## Input handling and web security

- [T1 launch] WEB-01 Server-side validation covers every input, not only the form.
- [T1 launch] WEB-02 Only parameterized queries are used.
- [T1 launch] WEB-03 No XSS: no unsanitized raw HTML rendering; user HTML or markdown is sanitized.
- [T1 launch] WEB-04 CSRF: state-changing handlers check origin or use tokens.
- [T0 launch] WEB-05 No SSRF on server-side URL fetches (internal and metadata addresses blocked) and no open redirects.
- [T1 100] WEB-06 Text fields handle apostrophes (O'Brien), accents, emoji and right-to-left text in storage, display, search and email.
- [T0 launch] WEB-07 Security headers are on every response: HSTS, X-Content-Type-Options, frame-ancestors (or X-Frame-Options), Referrer-Policy.
- [T1 launch] (gate: accounts) WEB-08 CORS never reflects an arbitrary Origin together with `Access-Control-Allow-Credentials: true`; authenticated routes use an explicit origin allowlist.
- [T1 100] WEB-09 Duplicate submits are blocked in the UI (button disabled while pending). Server-side idempotency is covered by BILL-02, BILL-12 and JOB-04.
- [T1 launch] WEB-10 Every endpoint that spends money or sends something (AI, email, SMS, upload, outbound fetch) is rate-limited per user and per IP. AUTH-05 covers only login and reset.
- [T1 10] WEB-11 Route handlers and outbound HTTP calls have server-side timeouts, and request bodies have size limits.
- [A] WEB-12 A Content-Security-Policy with default-src or script-src is set on HTML responses.

## File uploads (gate: uploads)

- [T0 launch] UPL-01 Size and type limits are enforced on the server.
- [T2] UPL-02 Files are malware-scanned before being served to other users. Applies at T1 when files are served to other users.
- [T0 launch] UPL-03 Files are stored where they cannot execute: private bucket unless intended, short-lived signed URLs, separate origin or `Content-Disposition: attachment`.
- [T1 10] UPL-04 Lifecycle rules delete temp files and old uploads.
- [T0 launch] (gate: uploads) UPL-05 Public buckets cannot be listed, and signed upload URLs are scoped to key, content type and size.

## Email (gate: transactional email)

- [T0 launch] MAIL-02 Per sending domain: production sending access (not a sandbox or trial), DKIM verified and aligned with the From domain (SPF alignment optional), DMARC with quarantine or reject, and, once there are 30 days of sending, bounce rate under 5% and complaint rate under 0.1% (N/A under 30 days of sending). `p=none` is FAIL at T1 and PASS with a note at T0 if `rua` is set.
- [T0 launch] MAIL-03 Bounces and complaints suppress the address (provider-managed suppression or your own handler).
- [T0 launch] MAIL-05 Email sending credentials are scoped to sending only, restricted to verified identities, and used by nothing else.
- [T1 100] MAIL-06 Alarms exist on bounce rate, complaint rate and send volume.
- [A] MAIL-07 Emails render correctly in Gmail, Outlook (desktop and web) and Apple Mail, including dark mode.
- [T1 10] MAIL-08 Users can turn notifications off per category and unsubscribe links work.

## Background jobs, cron and caching (gate: background jobs)

- [T1 100] JOB-01 Slow work runs in a job queue, not inside the request.
- [T1 10] JOB-02 Failed jobs retry with exponential backoff and jitter, and jobs that exhaust retries alert a human.
- [T2] JOB-03 A dead-letter queue catches jobs that exhaust retries, with alerting on it.
- [T0 launch] JOB-04 Every cron job is idempotent and lock-protected, so overlap or a second server cannot double-run it.

## Deploys and environments

- [T1 100] DEP-01 Dev, staging and production match: same database version, same env var set, same build.
- [T2] DEP-02 Infrastructure and server setup are stored as code, not in one person's memory. At T1 an attested setup document is accepted under CODE-09.
- [T1 10] DEP-03 The deploy pipeline runs tests before anything reaches production.
- [T0 launch] DEP-04 One-step rollback exists and has been used or drilled. A platform one-click rollback is PASS without a drill; self-built deploys need a dated rollback record.
- [T2] DEP-05 Deploys are zero-downtime: health-checked switchover and migrations compatible with old and new code.
- [T2] DEP-06 Feature flags let code ship without shipping the feature.
- [T1 100] DEP-07 Caches are invalidated when the underlying data changes, including CDN cache and framework revalidation.
- [T1 10] DEP-08 Production env is validated against a schema at build or boot: a missing or malformed variable fails the deploy, not a request.
- [T1 launch] DEP-09 Preview and staging deployments do not use the production database or live keys, or they sit behind auth.

## Observability and incident response

- [T0 launch] OBS-01 External uptime checks run from outside your hosting provider.
- [T0 launch] OBS-02 Error tracking groups errors instead of one email per occurrence.
- [T0 launch] OBS-03 Cost alerts fire the same day on every cloud account and hosting provider, plus a hard spend cap where the provider offers one.
- [T1 launch] (gate: AI/LLM) OBS-04 Every paid AI API key has a spend cap or alert.
- [T1 10] OBS-05 Logs are structured and searchable, record who did what, and exclude passwords, tokens and card numbers.
- [T1 10] OBS-06 Alerts reach a human, with fewer than 3 non-actionable firings in 30 days.
- [T2] OBS-07 Dashboards show latency, error rate and throughput.
- [T2] OBS-08 Tracing follows a single slow request across services.
- [T1 10] OBS-09 Alerts reach a phone: a test alert pages a person outside working hours. (attest)
- [T2] OBS-10 Postmortems follow incidents and each produces at least one concrete change. (attest)
- [T2] OBS-11 A public status page exists.
- [T1 10] OBS-12 A health endpoint exists, is not cached, touches the database, and uptime checks hit it.
- [T2] OBS-13 Someone is on call with a named backup, and runbooks explain each alert and what to do. (attest)
- [T1 10] (gate: AI/LLM) OBS-14 Where AI usage is per customer, a per-customer cap is enforced.

## Testing

- [T1 100] TEST-01 Unit tests cover core business logic.
- [T2] TEST-02 Integration tests exist for every third-party API the app depends on.
- [T1 10] TEST-03 End-to-end tests cover signup, login, checkout and every flow that makes money, including a payments integration test.
- [A] TEST-04 Each bug fix adds a regression test.
- [T2] TEST-05 Load testing is done before launches or big email sends.
- [T2] TEST-06 Test and staging data looks like production without being production data.

## Billing (gate: payments)

- [T1 launch] BILL-01 Incoming payment webhook signatures are verified.
- [T1 launch] BILL-02 Idempotency keys are used on every direct charge or refund call; with hosted checkout, refund calls carry keys and event dedupe is verified under BILL-12.
- [T1 10] BILL-03 Subscriptions, upgrades, downgrades and proration are correct.
- [T1 10] BILL-04 Failed payments are retried and dunning emails are sent.
- [T2] BILL-05 Refunds, credits and partial refunds work.
- [T1 launch] BILL-06 Tax obligations for your current customers' countries are known and handled (merchant of record, a tax engine, or a written accountant decision). (attest)
- [T1 100] (gate: usage billing) BILL-07 Usage metering matches what is billed, to the cent, and is reconciled at least monthly. (attest)
- [T1 launch] BILL-08 Card numbers never touch your database or logs.
- [T2] BILL-09 Invoices carry the customer's PO number.
- [T2] BILL-10 Annual contracts with custom terms are supported without code forks.
- [T2] BILL-11 Revenue reports reconcile against the payment processor.
- [T1 launch] BILL-12 Payment webhooks are deduplicated on event id and out-of-order events are handled: a later subscription update is never overwritten by an earlier retry.
- [T1 launch] BILL-13 Paid access is derived from provider state (webhook plus periodic reconciliation), never from the checkout success redirect.
- [T1 launch] BILL-14 Card-testing protection: fraud rules, or a rate limit plus bot challenge, on payment-intent, setup-intent and checkout-session creation. Applies when anonymous visitors can create payment objects; N/A for invoice-only or logged-in hosted checkout with provider fraud rules on.
- [T1 10] BILL-15 Dispute and chargeback events alert a human, and the dispute rate is watched (card networks and the provider can close accounts at about 1%).
- [T1 launch] (gate: subscriptions) BILL-16 Customers can cancel in the app without contacting support (auto-renewal laws).
- [T1 launch] BILL-17 A live-mode webhook endpoint is registered for the production URL, subscribed to every event the handler uses, and signed with the live secret; recent deliveries return 2xx. The provider account is activated with payouts enabled.

## Public API and integrations (gate: API, webhooks or integrations)

- [T1 10] API-01 Outgoing webhooks are signed, retried with backoff, and failed deliveries are logged where customers can see them.
- [T2] API-02 The API is versioned. Applies at T1 when the API is public.
- [T1 10] API-03 Customers can create, scope and revoke API keys; keys are stored hashed.
- [T2] API-04 API rate limits are documented and returned in headers. Applies at T1 when the API is public.
- [T1 10] API-05 OAuth tokens are refreshed before expiry, with graceful re-auth when refresh fails.
- [T1 100] API-06 Third-party responses are validated against a schema so unannounced format changes fail loudly.
- [T2] API-07 A sandbox environment lets customers test their integrations.

## AI features (gate: AI/LLM)

- [T1 launch] AI-01 Every tool the model can call runs with the requesting user's permissions and tenant scope.
- [T1 100] AI-04 Evals run on every model or prompt change.
- [T1 10] AI-05 A fallback exists for when the model provider is down.
- [T1 10] AI-06 Model IDs are pinned and tracked for deprecation dates, with a migration plan.
- [T1 100] AI-07 Prompt and output logs exist for debugging, excluding what you are not allowed to keep.
- [T1 launch] AI-08 Human approval is required before any AI action that cannot be undone.

## UX and accessibility

- [A] UX-01 Every screen has empty, loading and error states.
- [A] UX-02 Error messages tell the user what to do.
- [A] UX-03 Forms keep what the user typed when one field fails validation.
- [T1 100] UX-04 Destructive or bulk actions can be undone: soft delete with a restore path where users delete things, and undo on bulk edits.
- [A] UX-05 The back button does what people expect, because the URL reflects state.
- [A] UX-06 Keyboard navigation and screen reader support work on core flows.
- [T2] UX-07 WCAG 2.2 AA (recommended; 2.1 AA is the common contractual floor) is met where contracts or the European Accessibility Act require it. The Act exempts microenterprises providing services.
- [T1 100] (gate: multiple locales) UX-08 Translations are complete, and dates, numbers and currencies are formatted per locale.
- [A] UX-09 Pages load acceptably on poor connections such as hotel wifi, and the app works on the old hardware and browsers your customers use.

## Code health and documentation

- [A] CODE-01 Code is readable: clear names, small functions, lint and type checks clean.
- [A] CODE-02 No 2,000-line files; code is split by responsibility.
- [A] CODE-03 Dead code from abandoned agent attempts is removed.
- [A] CODE-04 Every change is reviewed (human or a separate reviewer agent that did not write it) before merge.
- [A] CODE-05 A branching strategy lets two people or agents work without overwriting each other.
- [A] CODE-07 Architecture decisions are written down with reasons.
- [A] CODE-09 A README gets a new engineer running locally in under an hour.
- [T1 100] CODE-10 Documentation exists for whoever inherits the app: architecture, deploy, secrets handling, operations and server setup. At T1 an attested setup document is accepted.

## Operations, compliance and ownership

- [T2] OPS-01 An admin panel covers ops tasks that would otherwise need direct DB access.
- [T2] OPS-02 Support tooling lets customer success answer questions without pinging engineering.
- [T2] OPS-03 A customer-facing changelog exists.
- [T0 launch] OPS-04 Terms and privacy policy are published, linked from signup and checkout, and name the processors actually in use.
- [T1 100] OPS-05 A security.txt and vulnerability disclosure process exist.
- [T2] OPS-06 A penetration test happened before the customer's security team runs their own. (attest)
- [T2] OPS-07 Security questionnaire answers are backed by an evidence folder. (attest)
- [T2] OPS-08 SOC 2 is in place if the sales motion requires it. (attest)
- [T2] (gate: health data) OPS-09 HIPAA controls are in place and a BAA is signed with every vendor touching the data, including the model provider. (attest)
- [T2] (gate: card data) OPS-10 PCI scope is documented. (attest)
- [T2] OPS-11 DPAs are signed with hosting, CDN, email, cloud and every model provider. (attest)
- [T0 launch] OPS-12 Every account, domain, repo and cloud account (including cloud root, with MFA) is owned by the company or a shared org account with two admins; no critical account sits on a personal email or personal card.
- [T0 launch] OPS-13 Credential escrow: a second person, or a sealed emergency-access path (password-manager emergency kit, break-glass doc held by a trusted person), can reach every credential and MFA recovery code, and at least one other person can deploy and restore. (attest)
- [T0 launch] OPS-14 Third-party scripts are listed (analytics, chat, replay); cookie consent is in place where required; no personal data goes to analytics; session replay masks inputs.
- [A] OPS-15 A single-vendor dependency list exists (auth, database, hosting, email): what breaks when each is down, and confirmation that data can be exported from each. (attest)
- [T1 launch] OPS-16 A working support contact (email or form) reaches a human and is shown in the app and on receipts.
- [T1 100] OPS-17 A written lost-laptop plan lists keys to rotate, sessions to kill and the order. (attest)
- [T1 10] OPS-18 A customer-facing DPA and a public subprocessor list exist and match the vendors in use.
