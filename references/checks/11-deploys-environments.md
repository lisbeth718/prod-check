# Deploys and environments

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| DEP-01 | T1 | Dev, staging and production match: same database version, same env var set, same build. | Diff versions and env var names across environments. | Diff output |  |
| DEP-02 | T1 | Infrastructure and server setup are stored as code, not in one person's memory. | Find IaC, compose files or setup scripts; check they match reality. | Repo paths |  |
| DEP-03 | T1 | The deploy pipeline runs tests before anything reaches production. | Read pipeline config; confirm a failing test blocks deploy. | Pipeline lines |  |
| DEP-04 | T1 | One-step rollback exists and has been used or drilled. | Find the rollback command, script or runbook in the repo or deploy system; find a log or deploy history entry showing a rollback run with a date. No artifact means UNKNOWN, not PASS. | Command, runbook path, log entry with date |  |
| DEP-05 | T1 | Deploys are zero-downtime: health-checked switchover and migrations compatible with old and new code. | Read the deploy sequence; watch availability during a deploy in a test environment. | Sequence, probe output |  |
| DEP-06 | T1 | Feature flags let code ship without shipping the feature. | Find the flag mechanism and a flag in use. | Code lines |  |
| DEP-07 | T1 | Caches are invalidated when the underlying data changes, including CDN cache and framework revalidation. | Change data in a test environment and confirm all cache layers update. e.g. Next.js `revalidateTag`, CDN purge call. | Test result, code lines |  |
