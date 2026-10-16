# Deploys and environments

Gate: none (always in scope up to the app tier)

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| DEP-01 | T1 | 100 | Dev, staging and production match: same database version, same env var set, same build. | Diff versions and env var names across environments. | Diff output |  |
| DEP-02 | T2 | - | Infrastructure and server setup are stored as code, not in one person's memory. At T1 an attested setup document is accepted under CODE-09. | Find IaC, compose files or setup scripts; check they match reality. | Repo paths |  |
| DEP-03 | T1 | 10 | The deploy pipeline runs tests before anything reaches production. | Read pipeline config; confirm a failing test blocks deploy. | Pipeline lines |  |
| DEP-04 | T0 | - | One-step rollback exists and has been used or drilled. | Find the rollback command, script or runbook in the repo or deploy system; find a log or deploy history entry showing a rollback run with a date. No artifact means UNKNOWN, not PASS. | Command, runbook path, log entry with date |  |
| DEP-05 | T2 | - | Deploys are zero-downtime: health-checked switchover and migrations compatible with old and new code. | Read the deploy sequence; watch availability during a deploy in a test environment. | Sequence, probe output |  |
| DEP-06 | T2 | - | Feature flags let code ship without shipping the feature. | Find the flag mechanism and a flag in use. | Code lines |  |
| DEP-07 | T1 | 100 | Caches are invalidated when the underlying data changes, including CDN cache and framework revalidation. | Change data in a test environment and confirm all cache layers update. e.g. Next.js `revalidateTag`, CDN purge call. | Test result, code lines |  |
| DEP-08 | T1 | 10 | Production env is validated against a schema at build or boot: a missing or malformed variable fails the deploy, not a request. | Find the env schema parse at startup; build or boot a test environment with one required variable unset and confirm it fails. e.g. a zod parse of `process.env` at boot. | Code lines, boot output |  |
| DEP-09 | T1 | invoice | Preview and staging deployments do not use the production database or live keys, or they sit behind auth. | List env var names per environment (names only) and compare; confirm the preview database URL differs from production; request a preview URL while logged out. | Env name table per environment, preview auth result |  |
