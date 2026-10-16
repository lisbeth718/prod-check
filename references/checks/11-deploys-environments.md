# Deploys and environments

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| DEP-01 | T1 | Dev, staging and production match: same database version, same env var set, same build. | Diff versions and env var names across environments. | Diff output |  |
| DEP-02 | T1 | Infrastructure and server setup are stored as code, not in one person's memory. | Find IaC, compose files or setup scripts; check they match reality. | Repo paths |  |
| DEP-03 | T1 | The deploy pipeline runs tests before anything reaches production. | Read pipeline config; confirm a failing test blocks deploy. | Pipeline lines |  |
| DEP-04 | T1 | One-step rollback exists and has been tested. | Find the rollback command or button; ask for the last time it was used or drilled. | Command, last test date |  |
| DEP-05 | T1 | Deploys are zero-downtime: health-checked switchover and migrations compatible with old and new code. | Read the deploy sequence; watch availability during a deploy in a test environment. | Sequence, probe output |  |
| DEP-06 | T1 | Feature flags let code ship without shipping the feature. | Find the flag mechanism and a flag in use. | Code lines |  |
