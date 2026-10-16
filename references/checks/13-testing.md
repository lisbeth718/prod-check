# Testing

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| TEST-01 | T1 | Unit tests cover core business logic, written or reviewed by someone who knows what the logic should do, not only agent tests that assert current behavior. | Run the suite; read tests for money, permissions and core rules; check they assert intended rules. | Test run output, test excerpts |  |
| TEST-02 | T1 | Integration tests exist for every third-party API the app depends on. | List third-party APIs; map each to a test or contract fixture. | API-to-test table |  |
| TEST-03 | T1 | End-to-end tests cover signup, login, checkout and every flow that makes money. | List E2E specs; run them. e.g. Playwright. | Spec list, run output |  |
| TEST-04 | T1 | Each bug fix adds a regression test. | Sample recent bug-fix commits and look for a test in each. | Commit list |  |
| TEST-05 | T1 | Load testing is done before launches or big email sends. | Ask for the last load test and its numbers. | Report or results |  |
| TEST-06 | T1 | Test and staging data looks like production without being production data. | Compare staging data source; check for real personal data. | Data source, scan result |  |
| TEST-07 | T1 | Tested on slow networks and old hardware or browsers your customers use. | Run throttled profile and the oldest supported browser. | Throttled run result |  |
