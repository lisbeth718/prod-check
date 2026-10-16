# Quick profile

Quick mode runs and reports only the IDs below: 37 checks (plus 2 conditional) vs 166 in a full run, of which 9 are advisory.
Gates and the app tier still apply: a check whose gate is off is N/A, and a check above the app tier is N/A.
Advisory rows (tier A) are never run in quick mode. Attestation in quick mode covers only attest rows listed here.

| ID | File |
|---|---|
| SRV-01 | 01-server-network.md |
| SRV-02 | 01-server-network.md |
| SRV-06 | 01-server-network.md |
| SRV-09 | 01-server-network.md |
| SEC-01 | 02-secrets-supply-chain.md |
| SEC-02 | 02-secrets-supply-chain.md |
| SEC-03 | 02-secrets-supply-chain.md |
| SEC-04 | 02-secrets-supply-chain.md |
| SEC-06 | 02-secrets-supply-chain.md |
| SEC-08 | 02-secrets-supply-chain.md |
| AUTH-02 | 03-authentication.md |
| AUTH-05 | 03-authentication.md |
| AUTH-09 | 03-authentication.md |
| AUTH-12 | 03-authentication.md |
| AUTHZ-01 | 04-authorization-tenancy.md |
| AUTHZ-03 | 04-authorization-tenancy.md |
| DB-17 | 05-database-data-model.md |
| BKP-01 | 06-backups-recovery.md |
| BKP-03 | 06-backups-recovery.md |
| WEB-01 | 07-input-web-security.md |
| WEB-02 | 07-input-web-security.md |
| WEB-03 | 07-input-web-security.md |
| WEB-07 | 07-input-web-security.md |
| WEB-10 | 07-input-web-security.md |
| MAIL-02 | 09-email.md |
| MAIL-03 | 09-email.md |
| JOB-04 | 10-jobs-cron-caching.md |
| DEP-04 | 11-deploys-environments.md |
| OBS-01 | 12-observability-incidents.md |
| OBS-03 | 12-observability-incidents.md |
| OBS-04 | 12-observability-incidents.md |
| OPS-12 | 19-ops-compliance-ownership.md |
| BILL-01 | 14-billing.md |
| BILL-02 | 14-billing.md |
| BILL-12 | 14-billing.md |
| BILL-13 | 14-billing.md |
| AI-01 | 16-ai-features.md |

Conditional (run only when the condition holds):

| ID | File | Run when |
|---|---|---|
| WEB-05 | 07-input-web-security.md | the AI/LLM gate or URL fetching is on |
| DB-18 | 05-database-data-model.md | row-level security is used |

BKP-02 and BKP-04 are not in the quick list: BKP-04 is folded into a note on BKP-01.
