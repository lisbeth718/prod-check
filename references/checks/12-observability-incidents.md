# Observability and incident response

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| OBS-01 | T0 | External uptime checks run from outside your hosting provider. | List monitors and their probe locations; confirm they alert someone. | Monitor list |  |
| OBS-02 | T0 | Error tracking groups errors instead of one email per occurrence. | Find the tracker, its DSN in use, and grouping. | Config, sample issue |  |
| OBS-03 | T0 | Cost alerts fire the same day on every cloud account and hosting provider. | List budgets and alerts with thresholds and recipients. | Alert list |  |
| OBS-04 | T0 | (gate: AI) A cost alert exists on every paid AI API key. | List AI keys in use; check each has a spend limit or alert in the provider console. | Key-to-alert table |  |
| OBS-05 | T1 | Logs are structured and searchable, record who did what, and exclude passwords, tokens and card numbers. | Sample logs; grep for secrets and tokens; check actor fields. | Log sample, grep output |  |
| OBS-06 | T1 | Alerts reach a human and are tuned until they stop firing for nothing. | List alert routes; read the last 30 days of firings. | Alert routes, firing counts |  |
| OBS-07 | T1 | Dashboards show latency, error rate and throughput. | Open the dashboards and confirm those three exist. | Dashboard link or screenshot |  |
| OBS-08 | T2 | Tracing follows a single slow request across services. | Find the trace instrumentation and find one trace. | Trace ID or sample |  |
| OBS-09 | T1 | Someone is on call and picks up; runbooks explain each alert and what to do. | Ask who is on call; read runbooks for the top alerts. | Rota, runbook links | attest |
| OBS-10 | T1 | Postmortems follow incidents and each produces at least one concrete change. | Ask for the last two postmortems. | Postmortem links | attest |
| OBS-11 | T2 | A public status page exists. | Find the status page URL and confirm it updates. | URL |  |
