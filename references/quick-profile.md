# Quick profile

Quick mode runs and reports only the IDs below: 59 checks (plus 2 conditional) vs 180 in a full run, of which 16 are advisory.
The list is the high-risk T0 checks plus every T1 check with stage `invoice` (the checks to pass before your first invoice).
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
| SEC-09 | 02-secrets-supply-chain.md |
| AUTH-02 | 03-authentication.md |
| AUTH-05 | 03-authentication.md |
| AUTH-09 | 03-authentication.md |
| AUTH-10 | 03-authentication.md |
| AUTH-12 | 03-authentication.md |
| AUTH-14 | 03-authentication.md |
| AUTHZ-01 | 04-authorization-tenancy.md |
| AUTHZ-02 | 04-authorization-tenancy.md |
| AUTHZ-03 | 04-authorization-tenancy.md |
| AUTHZ-07 | 04-authorization-tenancy.md |
| AUTHZ-08 | 04-authorization-tenancy.md |
| DB-06 | 05-database-data-model.md |
| DB-08 | 05-database-data-model.md |
| DB-17 | 05-database-data-model.md |
| BKP-01 | 06-backups-recovery.md |
| BKP-03 | 06-backups-recovery.md |
| WEB-01 | 07-input-web-security.md |
| WEB-02 | 07-input-web-security.md |
| WEB-03 | 07-input-web-security.md |
| WEB-04 | 07-input-web-security.md |
| WEB-07 | 07-input-web-security.md |
| WEB-08 | 07-input-web-security.md |
| WEB-10 | 07-input-web-security.md |
| UPL-03 | 08-file-uploads.md |
| UPL-05 | 08-file-uploads.md |
| MAIL-02 | 09-email.md |
| MAIL-03 | 09-email.md |
| JOB-04 | 10-jobs-cron-caching.md |
| DEP-04 | 11-deploys-environments.md |
| DEP-09 | 11-deploys-environments.md |
| OBS-01 | 12-observability-incidents.md |
| OBS-02 | 12-observability-incidents.md |
| OBS-03 | 12-observability-incidents.md |
| OBS-04 | 12-observability-incidents.md |
| BILL-01 | 14-billing.md |
| BILL-02 | 14-billing.md |
| BILL-06 | 14-billing.md |
| BILL-08 | 14-billing.md |
| BILL-12 | 14-billing.md |
| BILL-13 | 14-billing.md |
| BILL-14 | 14-billing.md |
| BILL-15 | 14-billing.md |
| BILL-16 | 14-billing.md |
| AI-01 | 16-ai-features.md |
| AI-08 | 16-ai-features.md |
| OPS-04 | 19-ops-compliance-ownership.md |
| OPS-12 | 19-ops-compliance-ownership.md |
| OPS-16 | 19-ops-compliance-ownership.md |

Conditional (run only when the condition holds):

| ID | File | Run when |
|---|---|---|
| WEB-05 | 07-input-web-security.md | the AI/LLM gate or URL fetching is on |
| DB-18 | 05-database-data-model.md | row-level security is used |

BKP-02 and BKP-04 are not in the quick list: BKP-04 is folded into a note on BKP-01.
