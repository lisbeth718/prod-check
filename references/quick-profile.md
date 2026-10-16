# Quick profile

Quick mode runs every T0 check plus the T1 checks below. A quick run is about 60 to 90 minutes of agent time. A full run (every check in scope) is about half a day.
Gates and the app tier still apply: a check whose gate is off is N/A, and a check above the app tier is N/A.
A quick run proves the worst failures are absent. It does not prove readiness. Say so in the report.

## T0 checks (all)

SRV-01, SRV-02, SRV-03, SRV-04, SRV-06, SRV-07, SEC-01, SEC-02, SEC-03, SEC-04, SEC-05, SEC-06, DB-17, BKP-01, BKP-03, BKP-04, WEB-05, WEB-06, WEB-07, WEB-08, UPL-04, MAIL-01, MAIL-02, MAIL-03, MAIL-04, MAIL-05, MAIL-06, JOB-04, OBS-01, OBS-02, OBS-03, OBS-04, CODE-08, OPS-04, OPS-12

## T1 checks (selected)

| Topic | ID |
|---|---|
| Email normalization | AUTH-01 |
| Reset tokens | AUTH-02 |
| Sessions, kill all | AUTH-04 |
| Login rate limit | AUTH-05 |
| IDOR | AUTHZ-01 |
| Tenant scoping | AUTHZ-03 |
| Server validation | WEB-01 |
| SQL injection | WEB-02 |
| XSS | WEB-03 |
| CSRF | WEB-04 |
| Point-in-time recovery | BKP-02 |
| Money as integers | DB-08 |
| Migrations reversible | DB-03 |
| Webhook signatures | BILL-01 |
| Idempotency keys | BILL-02 |
| Prompt injection | AI-01 |
| Tests before prod deploy | DEP-03 |
| Rollback | DEP-04 |

## Covered by the T0 list above

Secrets scan (SEC-01), package existence (SEC-05), backups (BKP-01), restore drill (BKP-03), error tracking (OBS-02), uptime (OBS-01), cost alerts (OBS-03, OBS-04).
