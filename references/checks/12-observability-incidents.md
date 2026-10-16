# Observability and incident response

Gate: none for the file. OBS-04 is gated per row on AI/LLM.

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| OBS-01 | T0 | External uptime checks run from outside your hosting provider. | List monitors and their probe locations; confirm they alert someone. | Monitor list |  |
| OBS-02 | T0 | Error tracking groups errors instead of one email per occurrence. | Find the tracker, its DSN in use, and grouping. | Config, sample issue |  |
| OBS-03 | T0 | Cost alerts fire the same day on every cloud account and hosting provider. | List budgets and alerts with thresholds and recipients. | Alert list |  |
| OBS-04 | T1 | (gate: AI/LLM) Every paid AI API key has a spend cap or alert, and where usage is per customer a per-customer cap is enforced. | List AI keys in use; check each has a spend limit or alert in the provider console; find per-customer usage accounting and the enforcing limit in code. | Key-to-limit table, code lines |  |
| OBS-05 | T1 | Logs are structured and searchable, record who did what, and exclude passwords, tokens and card numbers. | Sample logs; grep for secrets and tokens; check actor fields. | Log sample, grep output |  |
| OBS-06 | T1 | Alerts reach a human and are tuned until they stop firing for nothing. | List alert routes; read the last 30 days of firings. | Alert routes, firing counts |  |
| OBS-07 | T2 | Dashboards show latency, error rate and throughput. | Open the dashboards and confirm those three exist. | Dashboard link or screenshot |  |
| OBS-08 | T2 | Tracing follows a single slow request across services. | Find the trace instrumentation and find one trace. | Trace ID or sample |  |
| OBS-09 | T1 | Alerts reach a phone: a test alert pages a person outside working hours. | Ask who gets paged; fire a test alert and confirm it reaches a phone. | Alert route, test page record | attest |
| OBS-10 | T2 | Postmortems follow incidents and each produces at least one concrete change. | Ask for the last two postmortems. | Postmortem links | attest |
| OBS-11 | T2 | A public status page exists. | Find the status page URL and confirm it updates. | URL |  |
| OBS-12 | T1 | A health endpoint exists, is not cached, touches the database, and uptime checks hit it. | Request the endpoint and read cache headers; read the handler for a DB query; read the uptime monitor target. | Response headers, code lines, monitor URL |  |
| OBS-13 | T2 | Someone is on call with a named backup, and runbooks explain each alert and what to do. | Ask who is on call and who is the backup; read runbooks for the top alerts. | Rota, runbook links | attest |
