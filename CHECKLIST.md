# Ship check: checklist

Every check in one flat list, for humans. Tier: T0 = no accounts, payments or customer data; T1 = any of those; T2 = enterprise or regulated.
Do everything at or below your tier. Skip a section when its gate (a feature you do not have) is off. `(attest)` means a person answers it; an auditor cannot verify it from code.
How to verify each item, and what evidence to keep, is in `references/checks/`.

## Server and network

- [T0] SRV-01 The database is not reachable from the internet.
- [T0] SRV-02 The origin accepts web traffic only from the CDN/WAF (IP allowlist or tunnel), and CDN-to-origin TLS is fully verified.
- [T0] SRV-03 SSH is key-only, root password login is disabled, unattended security upgrades are on.
- [T0] SRV-04 Encryption in transit everywhere, including app-to-database traffic between hosts.
- [T1] SRV-05 Encryption at rest: encrypted volumes or encrypted backups, and encrypted object storage.
- [T0] SRV-06 Domains are on auto-renew with a valid payment method; certificate expiry is tracked with alerts before expiry, not on the day.
- [T0] SRV-07 A CDN caches static assets so the app loads fast far from the origin.
- [T2] SRV-08 A second region or standby exists only if a contract promises uptime one box cannot deliver; autoscaling is needed only if load testing shows it.

## Secrets and supply chain

- [T0] SEC-01 Secret scan over full git history is clean and `.env*` files are not committed.
- [T0] SEC-02 Secrets never appear in `NEXT_PUBLIC_*` variables or any client bundle.
- [T0] SEC-03 Every key pasted into an agent chat, MCP config, or committed config file has been rotated. (attest)
- [T0] SEC-04 Lockfile committed, production dependencies audited, automated dependency scanning on.
- [T0] SEC-05 Every declared package exists in its registry with a real repository and plausible history; unused packages removed.
- [T0] SEC-06 Least privilege for everyone, including agents: production DB URLs, SSH keys and cloud keys in agent or MCP configs are scoped to what the task needs.
- [T1] SEC-07 A written plan exists for a stolen laptop with production access: which keys to rotate, which sessions to kill, in what order. (attest)

## Authentication (gate: accounts)

- [T1] AUTH-01 Email normalization handles case and plus-addressing; login works for `name+tag@domain`.
- [T1] AUTH-02 Reset tokens are hashed in the DB, single-use and expiring.
- [T1] AUTH-03 MFA is available with a recovery path for a lost phone.
- [T1] AUTH-04 Sessions time out, and all sessions for one user can be killed at once.
- [T1] AUTH-05 Login and reset are rate-limited on the real client IP, not the proxy's IP.
- [T1] AUTH-06 Lockouts slow attackers (progressive delay, per account and per IP) without hard-locking real users, and an admin unlock path exists.
- [T2] AUTH-07 SSO (SAML and OIDC) is supported for customers who require it.
- [T2] AUTH-08 SCIM provisioning and deprovisioning removes access immediately when IT offboards someone.

## Authorization and tenancy (gate: accounts)

- [T1] AUTHZ-01 Every server action and route handler checks the session and that the record belongs to that user or tenant (no IDOR).
- [T1] AUTHZ-02 Roles and permissions go beyond admin / everyone else and are enforced on the server.
- [T1] (gate: multi-tenant) AUTHZ-03 Tenant scoping is enforced in the query layer or database row-level security, not only in the UI.
- [T1] AUTHZ-04 Admin "log in as user" for support exists and every use is written to an audit log.
- [T2] AUTHZ-05 An audit trail records who viewed a record, not only who changed it.
- [T2] AUTHZ-06 Quarterly access reviews are signed off by a named person. (attest)

## Database and data model

- [T1] DB-01 The schema was designed: normalized where it should be, constraints and foreign keys present, no obviously ad hoc tables.
- [T1] DB-02 The data model survives the second customer's "one small change": settings or configurable fields instead of hardcoded single-customer logic.
- [T1] DB-03 Migrations are versioned and reversible, tested in both directions.
- [T1] DB-04 Migrations take no long table locks.
- [T1] DB-05 Indexes exist on foreign keys and filtered or sorted columns, checked against production-sized data.
- [T1] DB-06 Connection pooling is in place with headroom under the DB connection limit across every app sharing the instance.
- [T1] DB-07 Pagination stays fast at depth (keyset or cursor, not large OFFSET).
- [T1] DB-08 Money is stored as integer minor units, never floats.
- [T1] (gate: payments) DB-09 Currency, rounding and tax rules are correct for every country sold into.
- [T1] DB-10 Timestamps are stored as timezone-aware types; user-facing schedules are computed in the user's timezone.
- [T1] DB-11 Soft deletes exist where users delete things.
- [T1] DB-12 Retention rules from contracts and the privacy policy are implemented as code that deletes on schedule.
- [T1] DB-13 GDPR deletion reaches every table, object storage, logs and the email suppression list where relevant, with a stated backup expiry.
- [T1] DB-14 Customers can export their data (CSV or XLSX).
- [T1] DB-15 Imports survive real-world CSVs: BOMs, Windows-1252, semicolon delimiters, Excel date formats, quoted newlines.
- [T2] DB-16 Data residency matches contracts: hosting location, object storage jurisdiction, email region and model provider region.
- [T0] DB-17 The database major version is still within upstream security support.

## Backups and recovery

- [T0] BKP-01 Nightly off-box backups exist, in a separate bucket and with credentials separate from the app.
- [T1] BKP-02 Continuous archiving enables point-in-time recovery.
- [T0] BKP-03 A restore has actually been performed; the date of the last drill is recorded. None ever is a FAIL. (attest)
- [T0] BKP-04 Every app sharing the same database instance has its own backup and restore drill, not just this app.
- [T2] BKP-05 A disaster recovery plan is written down and tested at least yearly. (attest)

## Input handling and web security

- [T1] WEB-01 Server-side validation covers every input, not only the form.
- [T1] WEB-02 Only parameterized queries are used.
- [T1] WEB-03 No XSS: no unsanitized raw HTML rendering; user HTML or markdown is sanitized.
- [T1] WEB-04 CSRF: state-changing handlers check origin or use tokens.
- [T0] WEB-05 The rest of the OWASP Top 10 is reviewed: SSRF on URL-fetching features, open redirects, insecure deserialization.
- [T0] WEB-06 Text fields handle apostrophes (O'Brien), accents, emoji and right-to-left text in storage, display, search and email.
- [T0] WEB-07 Security headers are on every response: CSP, HSTS, X-Content-Type-Options, frame-ancestors, Referrer-Policy.
- [T0] WEB-08 CORS is not `*` on anything authenticated.
- [T1] WEB-09 Duplicate submits are handled: disabled button plus server-side idempotency.

## File uploads (gate: uploads)

- [T1] UPL-01 Size and type limits are enforced on the server.
- [T1] UPL-02 Files are malware-scanned before being served to other users.
- [T1] UPL-03 Files are stored where they cannot execute: private bucket unless intended, short-lived signed URLs, separate origin or `Content-Disposition: attachment`.
- [T0] UPL-04 Lifecycle rules delete temp files and old uploads.

## Email (gate: transactional email)

- [T0] MAIL-01 The email account is out of the provider sandbox in the sending region.
- [T0] MAIL-02 Per sending domain: DKIM verified, a custom return-path (MAIL FROM) domain so SPF aligns, DMARC present and moving from none to quarantine or reject.
- [T0] MAIL-03 Every send uses a tracking configuration; bounce and complaint events reach a handler that stops mailing those addresses; account-level suppression is on.
- [T0] MAIL-04 Bounce rate is well under 5% and complaint rate well under 0.1%.
- [T0] MAIL-05 Email sending credentials are scoped to sending only, restricted to verified identities, and used by nothing else.
- [T0] MAIL-06 Alarms exist on bounce rate, complaint rate and send volume.
- [T1] MAIL-07 Emails render correctly in Gmail, Outlook (desktop and web) and Apple Mail, including dark mode.
- [T1] MAIL-08 Users can turn notifications off per category and unsubscribe links work.

## Background jobs, cron and caching (gate: background jobs)

- [T1] JOB-01 Slow work runs in a job queue, not inside the request.
- [T1] JOB-02 Failed jobs retry with exponential backoff and jitter.
- [T1] JOB-03 A dead-letter queue catches jobs that exhaust retries, with alerting on it.
- [T0] JOB-04 Every cron job is idempotent and lock-protected, so overlap or a second server cannot double-run it.
- [T1] JOB-05 Caches are invalidated when the underlying data changes, including CDN cache and framework revalidation.

## Deploys and environments

- [T1] DEP-01 Dev, staging and production match: same database version, same env var set, same build.
- [T1] DEP-02 Infrastructure and server setup are stored as code, not in one person's memory.
- [T1] DEP-03 The deploy pipeline runs tests before anything reaches production.
- [T1] DEP-04 One-step rollback exists and has been tested.
- [T1] DEP-05 Deploys are zero-downtime: health-checked switchover and migrations compatible with old and new code.
- [T1] DEP-06 Feature flags let code ship without shipping the feature.

## Observability and incident response

- [T0] OBS-01 External uptime checks run from outside your hosting provider.
- [T0] OBS-02 Error tracking groups errors instead of one email per occurrence.
- [T0] OBS-03 Cost alerts fire the same day on every cloud account and hosting provider.
- [T0] (gate: AI) OBS-04 A cost alert exists on every paid AI API key.
- [T1] OBS-05 Logs are structured and searchable, record who did what, and exclude passwords, tokens and card numbers.
- [T1] OBS-06 Alerts reach a human and are tuned until they stop firing for nothing.
- [T1] OBS-07 Dashboards show latency, error rate and throughput.
- [T2] OBS-08 Tracing follows a single slow request across services.
- [T1] OBS-09 Someone is on call and picks up; runbooks explain each alert and what to do. (attest)
- [T1] OBS-10 Postmortems follow incidents and each produces at least one concrete change. (attest)
- [T1] OBS-11 A public status page exists.

## Testing

- [T1] TEST-01 Unit tests cover core business logic, written or reviewed by someone who knows what the logic should do, not only agent tests that assert current behavior.
- [T1] TEST-02 Integration tests exist for every third-party API the app depends on.
- [T1] TEST-03 End-to-end tests cover signup, login, checkout and every flow that makes money.
- [T1] TEST-04 Each bug fix adds a regression test.
- [T1] TEST-05 Load testing is done before launches or big email sends.
- [T1] TEST-06 Test and staging data looks like production without being production data.
- [T1] TEST-07 Tested on slow networks and old hardware or browsers your customers use.

## Billing (gate: payments)

- [T1] BILL-01 Incoming payment webhook signatures are verified.
- [T1] BILL-02 Idempotency keys are used on every charge.
- [T1] BILL-03 Subscriptions, upgrades, downgrades and proration are correct.
- [T1] BILL-04 Failed payments are retried and dunning emails are sent.
- [T1] BILL-05 Refunds, credits and partial refunds work.
- [T1] BILL-06 VAT and sales tax are correct, including EU reverse charge for B2B.
- [T1] BILL-07 Usage metering matches what is billed, to the cent, and is reconciled at least monthly. (attest)
- [T1] BILL-08 Card numbers never touch your database or logs.
- [T2] BILL-09 Invoices carry the customer's PO number.
- [T2] BILL-10 Annual contracts with custom terms are supported without code forks.
- [T2] BILL-11 Revenue reports reconcile against the payment processor.

## Public API and integrations (gate: API, webhooks or integrations)

- [T1] API-01 Outgoing webhooks are signed, retried with backoff, and failed deliveries are logged where customers can see them.
- [T1] API-02 The API is versioned.
- [T1] API-03 Customers can create, scope and revoke API keys; keys are stored hashed.
- [T1] API-04 API rate limits are documented and returned in headers.
- [T1] API-05 OAuth tokens are refreshed before expiry, with graceful re-auth when refresh fails.
- [T1] API-06 Third-party responses are validated against a schema so unannounced format changes fail loudly.
- [T2] API-07 A sandbox environment lets customers test their integrations.

## AI features (gate: AI/LLM)

- [T1] AI-01 User-supplied text reaching a model cannot trigger privileged tool calls (prompt injection).
- [T1] AI-02 Customer and patient data is kept out of prompts beyond what the task needs; no regulated data goes to a provider without a DPA (and a BAA for US health data). (attest)
- [T1] AI-03 A per-customer model spend cap exists.
- [T1] AI-04 Evals run on every model or prompt change.
- [T1] AI-05 A fallback exists for when the model provider is down.
- [T1] AI-06 Model IDs are pinned and tracked for deprecation dates, with a migration plan.
- [T1] AI-07 Prompt and output logs exist for debugging, excluding what you are not allowed to keep.
- [T1] AI-08 Human approval is required before any AI action that cannot be undone.

## UX and accessibility

- [T1] UX-01 Every screen has empty, loading and error states.
- [T1] UX-02 Error messages tell the user what to do.
- [T1] UX-03 Forms keep what the user typed when one field fails validation.
- [T1] UX-04 Destructive or bulk actions can be undone.
- [T1] UX-05 The back button does what people expect, because the URL reflects state.
- [T1] UX-06 Keyboard navigation and screen reader support work on core flows.
- [T2] UX-07 WCAG 2.1 AA is met where contracts or the European Accessibility Act require it.
- [T1] (gate: multiple locales) UX-08 Translations are complete, and dates, numbers and currencies are formatted per locale.
- [T1] UX-09 Pages load acceptably on poor connections such as hotel wifi.

## Code health and documentation

- [T1] CODE-01 Code is readable by a human: clear names, small functions, lint and type checks clean.
- [T1] CODE-02 No 2,000-line files; code is split by responsibility.
- [T1] CODE-03 Dead code from abandoned agent attempts is removed.
- [T1] CODE-04 Every change is reviewed (human or a separate reviewer agent that did not write it) before merge.
- [T1] CODE-05 A branching strategy lets two people or agents work without overwriting each other.
- [T1] CODE-06 Dependencies are upgraded monthly; the framework is upgraded before the current version loses security support.
- [T1] CODE-07 Architecture decisions are written down with reasons.
- [T0] CODE-08 The README gets a new engineer running locally in under an hour.
- [T1] CODE-09 Documentation exists for whoever inherits the app.

## Operations, compliance and ownership

- [T1] OPS-01 An admin panel covers ops tasks that would otherwise need direct DB access.
- [T1] OPS-02 Support tooling lets customer success answer questions without pinging engineering.
- [T1] OPS-03 A customer-facing changelog exists.
- [T0] OPS-04 Terms and privacy policy match what the code actually does.
- [T1] OPS-05 A security.txt and vulnerability disclosure process exist.
- [T2] OPS-06 A penetration test happened before the customer's security team runs their own. (attest)
- [T2] OPS-07 Security questionnaire answers are backed by an evidence folder. (attest)
- [T2] OPS-08 SOC 2 is in place if the sales motion requires it. (attest)
- [T2] (gate: health data) OPS-09 HIPAA controls are in place and a BAA is signed with every vendor touching the data, including the model provider. (attest)
- [T2] (gate: card data) OPS-10 PCI scope is documented. (attest)
- [T2] OPS-11 DPAs are signed with hosting, CDN, email, cloud and every model provider. (attest)
- [T0] OPS-12 Every account, domain, repo and cloud account (including cloud root, with MFA) is owned by the company, not a person. (attest)
- [T1] OPS-13 Bus factor above one: credentials are in a shared vault and at least one other person can deploy and restore. (attest)
