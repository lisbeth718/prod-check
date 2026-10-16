# Ship check: checklist

Every check in one flat list, for humans. Tier: T0 = Launch (public, no accounts, no payments, no customer data); T1 = Paying (any of: accounts, customer data, payments); T2 = Enterprise or regulated.
[A] = advisory: reported as a note, never a FAIL. Do everything at or below your tier. Skip a section when its gate (a feature you do not have) is off. `(attest)` means a person answers it; an auditor cannot verify it from code.
A `multi-tenant` gate means an org, team or workspace entity where several users share records; per-user ownership alone is not multi-tenant. T2 regulated data means health, financial account, legal, member, union or political data; ordinary PII (CVs, emails) stays T1.
How to verify each item, and what evidence to keep, is in `references/checks/`.

## Server and network

- [T0] (gate: self-hosted servers, database) SRV-01 The database is not reachable from the internet.
- [T0] (gate: self-hosted servers) SRV-02 The origin accepts web traffic only from the CDN/WAF (IP allowlist or tunnel), and CDN-to-origin TLS is fully verified.
- [T0] (gate: self-hosted servers) SRV-03 SSH is key-only, root password login is disabled, unattended security upgrades are on.
- [T0] SRV-04 Encryption in transit everywhere, including app-to-database traffic between hosts.
- [T0] SRV-06 Domains are on auto-renew with a valid payment method; certificate expiry is tracked with alerts before expiry, not on the day.
- [T1] SRV-07 A CDN caches static assets so the app loads fast far from the origin.
- [T2] SRV-08 A second region or standby exists only if a contract promises uptime one box cannot deliver; autoscaling is needed only if load testing shows it.
- [T0] (gate: self-hosted servers) SRV-09 Disk-fill protection: log rotation (journald and container log driver size caps), a scheduled image and build-cache prune, and a disk alert at about 80%.
- [T0] (gate: self-hosted servers) SRV-10 Server timezone is UTC, the clock is NTP-synced, and containers have memory limits and a restart policy.

## Secrets and supply chain

- [T0] SEC-01 Secret scan over full git history is clean and env files are not committed.
- [T0] SEC-02 Secrets never appear in client-exposed environment variables or any client bundle.
- [T0] SEC-03 Every key pasted into an agent chat, MCP config, or committed config file has been rotated. (attest)
- [T0] SEC-04 Lockfile committed, production dependencies audited, automated dependency scanning on, and dependencies and the framework are upgraded before the framework version loses security support.
- [T0] SEC-05 Every declared package exists in its registry with a real repository and plausible history. Unused packages are an advisory note, not a FAIL.
- [T0] SEC-06 Least privilege for everyone, including agents: production DB URLs, SSH keys and cloud keys in agent or MCP configs are scoped to what the task needs.
- [T0] SEC-08 Live and test credentials are separated per environment: no test-mode or sandbox keys in production, no live keys in dev or staging.

## Authentication (gate: accounts)

- [T1] AUTH-01 Emails are normalized by lowercasing and trimming, the `+tag` is kept, and uniqueness is enforced by a unique index on `lower(email)` or a case-insensitive type; login works for `name+tag@domain`.
- [T1] AUTH-02 Reset tokens are hashed in the DB, single-use and expiring.
- [T1] AUTH-03 MFA is available and enforced for admin accounts, with a recovery path for a lost device.
- [T1] AUTH-04 Sessions time out, and all sessions for one user can be killed at once.
- [T1] AUTH-05 Login and reset are rate-limited on the real client IP, not the proxy's IP.
- [T2] AUTH-06 Lockouts slow attackers (progressive delay, per account and per IP) without hard-locking real users, and an admin unlock path exists.
- [T2] AUTH-07 SSO (SAML and OIDC) is supported for customers who require it.
- [T2] AUTH-08 SCIM provisioning and deprovisioning removes access immediately when IT offboards someone.
- [T1] AUTH-09 Passwords are hashed with a password hash (argon2id, bcrypt or scrypt) at a sane cost.
- [T1] AUTH-10 Session cookies set HttpOnly, Secure and SameSite=Lax or Strict.
- [T2] AUTH-11 Login, password reset and signup do not reveal whether an account exists: same response and similar timing for existing and missing accounts.
- [T1] AUTH-12 Signup and free-tier abuse controls: bot challenge, disposable-email block, per-IP signup limit, and email verification before any costly action.
- [T2] AUTH-13 MFA is available to all users, with a recovery path for a lost phone.

## Authorization and tenancy (gate: accounts)

- [T1] AUTHZ-01 Every server action and route handler checks the session and that the record belongs to that user or tenant (no IDOR).
- [T1] AUTHZ-02 Roles and permissions are enforced on the server, in handlers or the data layer, not only by hiding UI.
- [T1] (gate: multi-tenant) AUTHZ-03 Tenant scoping is enforced in the query layer or database row-level security, not only in the UI.
- [T1] (gate: impersonation exists) AUTHZ-04 Admin "log in as user" for support writes every use to an audit log.
- [T2] AUTHZ-05 An audit trail records who viewed a record, not only who changed it.
- [T2] AUTHZ-06 Quarterly access reviews are signed off by a named person. (attest)

## Database and data model

- [A] DB-01 The schema was designed: normalized where it should be, constraints and foreign keys present, no obviously ad hoc tables.
- [A] DB-02 The data model survives the second customer's "one small change": settings or configurable fields instead of hardcoded single-customer logic.
- [T1] DB-03 Migrations are versioned and applied in order; forward-only is acceptable as long as a restore has been tested.
- [T1] DB-04 Migrations take no long table locks.
- [T1] DB-05 Indexes exist on foreign keys and filtered or sorted columns, checked against production-sized data.
- [T1] DB-06 Connection pooling is in place with headroom under the DB connection limit across every app sharing the instance.
- [T2] DB-07 Pagination stays fast at depth (keyset or cursor, not large OFFSET).
- [T1] DB-08 Money is stored as integer minor units; never as floating point.
- [T1] DB-10 Timestamps are stored as timezone-aware types; user-facing schedules are computed in the user's timezone.
- [T1] DB-13 GDPR deletion reaches every table, object storage, logs and the email suppression list where relevant, with a stated backup expiry.
- [T2] DB-14 Customers can export their data (CSV or XLSX).
- [T1] (gate: imports) DB-15 Imports survive real-world CSVs: BOMs, Windows-1252, semicolon delimiters, Excel date formats, quoted newlines.
- [T2] DB-16 Data residency matches contracts: hosting location, object storage jurisdiction, email region and model provider region.
- [T0] (gate: database) DB-17 The database major version is still within upstream security support.
- [T1] (gate: database) DB-18 The application's database role is not a superuser or the table owner, has no BYPASSRLS, and holds least-privilege grants.
- [T1] DB-19 Tables that grow without bound (sessions, logs, events, jobs, AI transcripts) have TTL or pruning, retention rules from contracts and the privacy policy run as scheduled deletion code, and database size is alerted.

## Backups and recovery (gate: database)

- [T0] BKP-01 Nightly off-box backups exist and are encrypted, in a separate bucket and with credentials separate from the app.
- [T1] BKP-02 Continuous archiving enables point-in-time recovery.
- [T0] BKP-03 A restore has actually been performed; the date of the last drill is recorded. (attest)
- [T0] BKP-04 Every app sharing the same database instance has its own backup and restore drill, not just this app.
- [T2] BKP-05 A disaster recovery plan is written down and tested at least yearly. (attest)

## Input handling and web security

- [T1] WEB-01 Server-side validation covers every input, not only the form.
- [T1] WEB-02 Only parameterized queries are used.
- [T1] WEB-03 No XSS: no unsanitized raw HTML rendering; user HTML or markdown is sanitized.
- [T1] WEB-04 CSRF: state-changing handlers check origin or use tokens.
- [T0] WEB-05 The rest of the OWASP Top 10 is reviewed: SSRF on URL-fetching features, open redirects, insecure deserialization.
- [T0] WEB-06 Text fields handle apostrophes (O'Brien), accents, emoji and right-to-left text in storage, display, search and email.
- [T0] WEB-07 Security headers are on every response: CSP (with default-src or script-src), HSTS, X-Content-Type-Options, frame-ancestors, Referrer-Policy.
- [T1] (gate: accounts) WEB-08 CORS never reflects an arbitrary Origin together with `Access-Control-Allow-Credentials: true`; authenticated routes use an explicit origin allowlist.
- [T1] WEB-09 Duplicate submits are blocked in the UI (button disabled while pending). Server-side idempotency is covered by BILL-02, BILL-12 and JOB-04.
- [T1] WEB-10 Every endpoint that spends money or sends something (AI, email, SMS, upload, outbound fetch) is rate-limited per user and per IP. AUTH-05 covers only login and reset.
- [T1] WEB-11 Route handlers and outbound HTTP calls have server-side timeouts, and request bodies have size limits.

## File uploads (gate: uploads)

- [T0] UPL-01 Size and type limits are enforced on the server.
- [T2] UPL-02 Files are malware-scanned before being served to other users. Applies at T1 when files are served to other users.
- [T0] UPL-03 Files are stored where they cannot execute: private bucket unless intended, short-lived signed URLs, separate origin or `Content-Disposition: attachment`.
- [T1] UPL-04 Lifecycle rules delete temp files and old uploads.
- [T0] (gate: uploads) UPL-05 Public buckets cannot be listed, and signed upload URLs are scoped to key, content type and size.

## Email (gate: transactional email)

- [T0] MAIL-02 Per sending domain: production sending access (not a sandbox or trial), DKIM verified, a custom return-path (MAIL FROM) domain so SPF aligns, DMARC with quarantine or reject, and over 30 days bounce rate under 5% and complaint rate under 0.1%. `p=none` is FAIL at T1 and PASS with a note at T0 if `rua` is set.
- [T0] MAIL-03 Every send uses a tracking configuration; bounce and complaint events reach a handler that stops mailing those addresses; account-level suppression is on.
- [T0] MAIL-05 Email sending credentials are scoped to sending only, restricted to verified identities, and used by nothing else.
- [T0] MAIL-06 Alarms exist on bounce rate, complaint rate and send volume.
- [A] MAIL-07 Emails render correctly in Gmail, Outlook (desktop and web) and Apple Mail, including dark mode.
- [T1] MAIL-08 Users can turn notifications off per category and unsubscribe links work.

## Background jobs, cron and caching (gate: background jobs)

- [T1] JOB-01 Slow work runs in a job queue, not inside the request.
- [T1] JOB-02 Failed jobs retry with exponential backoff and jitter, and jobs that exhaust retries alert a human.
- [T2] JOB-03 A dead-letter queue catches jobs that exhaust retries, with alerting on it.
- [T0] JOB-04 Every cron job is idempotent and lock-protected, so overlap or a second server cannot double-run it.

## Deploys and environments

- [T1] DEP-01 Dev, staging and production match: same database version, same env var set, same build.
- [T2] DEP-02 Infrastructure and server setup are stored as code, not in one person's memory. At T1 an attested setup document is accepted under CODE-09.
- [T1] DEP-03 The deploy pipeline runs tests before anything reaches production.
- [T0] DEP-04 One-step rollback exists and has been used or drilled.
- [T2] DEP-05 Deploys are zero-downtime: health-checked switchover and migrations compatible with old and new code.
- [T2] DEP-06 Feature flags let code ship without shipping the feature.
- [T1] DEP-07 Caches are invalidated when the underlying data changes, including CDN cache and framework revalidation.
- [T1] DEP-08 Production env is validated against a schema at build or boot: a missing or malformed variable fails the deploy, not a request.

## Observability and incident response

- [T0] OBS-01 External uptime checks run from outside your hosting provider.
- [T0] OBS-02 Error tracking groups errors instead of one email per occurrence.
- [T0] OBS-03 Cost alerts fire the same day on every cloud account and hosting provider.
- [T1] (gate: AI/LLM) OBS-04 Every paid AI API key has a spend cap or alert, and where usage is per customer a per-customer cap is enforced.
- [T1] OBS-05 Logs are structured and searchable, record who did what, and exclude passwords, tokens and card numbers.
- [T1] OBS-06 Alerts reach a human and are tuned until they stop firing for nothing.
- [T2] OBS-07 Dashboards show latency, error rate and throughput.
- [T2] OBS-08 Tracing follows a single slow request across services.
- [T1] OBS-09 Alerts reach a phone: a test alert pages a person outside working hours. (attest)
- [T2] OBS-10 Postmortems follow incidents and each produces at least one concrete change. (attest)
- [T2] OBS-11 A public status page exists.
- [T1] OBS-12 A health endpoint exists, is not cached, touches the database, and uptime checks hit it.
- [T2] OBS-13 Someone is on call with a named backup, and runbooks explain each alert and what to do. (attest)

## Testing

- [T1] TEST-01 Unit tests cover core business logic, written or reviewed by someone who knows what the logic should do, not only agent tests that assert current behavior.
- [T2] TEST-02 Integration tests exist for every third-party API the app depends on.
- [T1] TEST-03 End-to-end tests cover signup, login, checkout and every flow that makes money, including a payments integration test.
- [T1] TEST-04 Each bug fix adds a regression test.
- [T2] TEST-05 Load testing is done before launches or big email sends.
- [T2] TEST-06 Test and staging data looks like production without being production data.

## Billing (gate: payments)

- [T1] BILL-01 Incoming payment webhook signatures are verified.
- [T1] BILL-02 Idempotency keys are used on every direct charge or refund call; with hosted checkout, refund calls carry keys and event dedupe is verified under BILL-12.
- [T1] BILL-03 Subscriptions, upgrades, downgrades and proration are correct.
- [T1] BILL-04 Failed payments are retried and dunning emails are sent.
- [T2] BILL-05 Refunds, credits and partial refunds work.
- [T1] BILL-06 A tax engine or merchant of record is configured for every country sold into (VAT, sales tax, EU reverse charge for B2B). (attest)
- [T1] (gate: usage billing) BILL-07 Usage metering matches what is billed, to the cent, and is reconciled at least monthly. (attest)
- [T1] BILL-08 Card numbers never touch your database or logs.
- [T2] BILL-09 Invoices carry the customer's PO number.
- [T2] BILL-10 Annual contracts with custom terms are supported without code forks.
- [T2] BILL-11 Revenue reports reconcile against the payment processor.
- [T1] BILL-12 Payment webhooks are deduplicated on event id and out-of-order events are handled: a later subscription update is never overwritten by an earlier retry.
- [T1] BILL-13 Paid access is derived from provider state (webhook plus periodic reconciliation), never from the checkout success redirect.

## Public API and integrations (gate: API, webhooks or integrations)

- [T1] API-01 Outgoing webhooks are signed, retried with backoff, and failed deliveries are logged where customers can see them.
- [T2] API-02 The API is versioned. Applies at T1 when the API is public.
- [T1] API-03 Customers can create, scope and revoke API keys; keys are stored hashed.
- [T2] API-04 API rate limits are documented and returned in headers. Applies at T1 when the API is public.
- [T1] API-05 OAuth tokens are refreshed before expiry, with graceful re-auth when refresh fails.
- [T1] API-06 Third-party responses are validated against a schema so unannounced format changes fail loudly.
- [T2] API-07 A sandbox environment lets customers test their integrations.

## AI features (gate: AI/LLM)

- [T1] AI-01 User-supplied text reaching a model cannot trigger privileged tool calls (prompt injection).
- [T1] AI-04 Evals run on every model or prompt change.
- [T1] AI-05 A fallback exists for when the model provider is down.
- [T1] AI-06 Model IDs are pinned and tracked for deprecation dates, with a migration plan.
- [T1] AI-07 Prompt and output logs exist for debugging, excluding what you are not allowed to keep.
- [T1] AI-08 Human approval is required before any AI action that cannot be undone.

## UX and accessibility

- [T1] UX-01 Every screen has empty, loading and error states.
- [T1] UX-02 Error messages tell the user what to do.
- [T1] UX-03 Forms keep what the user typed when one field fails validation.
- [T1] UX-04 Destructive or bulk actions can be undone: soft delete with a restore path where users delete things, and undo on bulk edits.
- [A] UX-05 The back button does what people expect, because the URL reflects state.
- [T1] UX-06 Keyboard navigation and screen reader support work on core flows.
- [T2] UX-07 WCAG 2.2 AA (recommended; 2.1 AA is the common contractual floor) is met where contracts or the European Accessibility Act require it. The Act exempts microenterprises providing services.
- [T1] (gate: multiple locales) UX-08 Translations are complete, and dates, numbers and currencies are formatted per locale.
- [A] UX-09 Pages load acceptably on poor connections such as hotel wifi, and the app works on the old hardware and browsers your customers use.

## Code health and documentation

- [T1] CODE-01 Code is readable by a human: clear names, small functions, lint and type checks clean.
- [A] CODE-02 No 2,000-line files; code is split by responsibility.
- [A] CODE-03 Dead code from abandoned agent attempts is removed.
- [T1] CODE-04 Every change is reviewed (human or a separate reviewer agent that did not write it) before merge.
- [A] CODE-05 A branching strategy lets two people or agents work without overwriting each other.
- [A] CODE-07 Architecture decisions are written down with reasons.
- [T1] CODE-09 Documentation exists for whoever inherits the app: architecture, deploy, secrets handling, operations and server setup, and a README that gets a new engineer running locally in under an hour. At T1 an attested setup document is accepted.

## Operations, compliance and ownership

- [T2] OPS-01 An admin panel covers ops tasks that would otherwise need direct DB access.
- [T2] OPS-02 Support tooling lets customer success answer questions without pinging engineering.
- [T2] OPS-03 A customer-facing changelog exists.
- [T0] OPS-04 Terms and privacy policy match what the code actually does.
- [T1] OPS-05 A security.txt and vulnerability disclosure process exist.
- [T2] OPS-06 A penetration test happened before the customer's security team runs their own. (attest)
- [T2] OPS-07 Security questionnaire answers are backed by an evidence folder. (attest)
- [T2] OPS-08 SOC 2 is in place if the sales motion requires it. (attest)
- [T2] (gate: health data) OPS-09 HIPAA controls are in place and a BAA is signed with every vendor touching the data, including the model provider. (attest)
- [T2] (gate: card data) OPS-10 PCI scope is documented. (attest)
- [T2] OPS-11 DPAs are signed with hosting, CDN, email, cloud and every model provider. (attest)
- [T0] OPS-12 Every account, domain, repo and cloud account (including cloud root, with MFA) is owned by the company, not a person.
- [T0] OPS-13 Credential escrow: every credential is reachable by a second person or a vault, at least one other person can deploy and restore, and a written lost-laptop plan lists keys to rotate, sessions to kill and the order. (attest)
- [T0] OPS-14 Third-party scripts are listed (analytics, chat, replay); cookie consent is in place where required; no personal data goes to analytics; session replay masks inputs.
- [T1] OPS-15 A single-vendor dependency list exists (auth, database, hosting, email): what breaks when each is down, and confirmation that data can be exported from each. (attest)
