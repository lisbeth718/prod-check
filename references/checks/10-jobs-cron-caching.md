# Background jobs, cron and caching

Gate: background jobs

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| JOB-01 | T1 | 100 | Slow work runs in a job queue, not inside the request. | Find handlers that call slow work (email, exports, third-party APIs, AI) inline; time them. | Handler list, timings |  |
| JOB-02 | T1 | 10 | Failed jobs retry with exponential backoff and jitter, and jobs that exhaust retries alert a human. | Read queue retry config; find the alert on exhausted jobs and fail a test job to see it fire. | Config values, alert rule |  |
| JOB-03 | T2 | - | A dead-letter queue catches jobs that exhaust retries, with alerting on it. | Find the DLQ and its alert rule; check it is empty or triaged. | Config, alert rule |  |
| JOB-04 | T0 | launch | Every cron job is idempotent and lock-protected, so overlap or a second server cannot double-run it. | List cron jobs on every server; read each for a lock and safe re-run. e.g. Postgres `pg_try_advisory_lock`. | Job list, code lines |  |
