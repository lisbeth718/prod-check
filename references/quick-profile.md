# Quick profile

Quick mode runs and reports only the IDs below: roughly 30 checks vs ~160 in a full run.
Gates and the app tier still apply: a check whose gate is off is N/A, and a check above the app tier is N/A.
Gated checks (SRV-01, SRV-02 on self-hosted servers; DB-17, BKP-* on database; AUTH-*, AUTHZ-* on accounts; BILL-* on payments; MAIL-* on transactional email; JOB-04 on background jobs; AI-01 on AI/LLM) apply only if the gate is on.

SRV-01, SRV-02, SEC-01, SEC-02, SEC-04, SEC-06, DB-17, BKP-01, BKP-02, BKP-03, BKP-04, WEB-07, MAIL-02, MAIL-03, JOB-04, OBS-01, OBS-03, OPS-12, AUTH-02, AUTH-05, AUTH-09, AUTHZ-01, AUTHZ-03, WEB-01, WEB-02, WEB-03, BILL-01, BILL-02, AI-01
