# Background jobs, cron and caching

Gate: background jobs

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| JOB-01 | T1 | Slow work runs in a job queue, not inside the request. | Find handlers that call slow work (email, exports, third-party APIs, AI) inline; time them. | Handler list, timings |  |
| JOB-02 | T1 | Failed jobs retry with exponential backoff and jitter. | Read queue retry config. | Config values |  |
| JOB-03 | T1 | A dead-letter queue catches jobs that exhaust retries, with alerting on it. | Find the DLQ and its alert rule; check it is empty or triaged. | Config, alert rule |  |
| JOB-04 | T0 | Every cron job is idempotent and lock-protected, so overlap or a second server cannot double-run it. | List cron jobs on every server; read each for a lock and safe re-run. e.g. Postgres `pg_try_advisory_lock`. | Job list, code lines |  |

<!-- Retired IDs: JOB-05 (moved to DEP-07). Numbers are not reused. -->
