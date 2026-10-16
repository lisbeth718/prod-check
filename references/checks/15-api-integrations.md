# Public API and integrations

Gate: API, webhooks or integrations

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| API-01 | T1 | 10 | Outgoing webhooks are signed, retried with backoff, and failed deliveries are logged where customers can see them. | Read the sender; fail a test endpoint and watch retries and log. | Code lines, test result |  |
| API-02 | T2 | - | The API is versioned. Applies at T1 when the API is public. | Check the URL or header version and a deprecation policy. | Route lines |  |
| API-03 | T1 | 10 | Customers can create, scope and revoke API keys; keys are stored hashed. | Read key schema and UI/API; revoke a key and retry it. | Schema lines, test result |  |
| API-04 | T2 | - | API rate limits are documented and returned in headers. Applies at T1 when the API is public. | Exceed a limit in a test environment and read headers and docs. | Response headers |  |
| API-05 | T1 | 10 | OAuth tokens are refreshed before expiry, with graceful re-auth when refresh fails. | Read refresh logic; expire a token in a test environment. | Code lines, test result |  |
| API-06 | T1 | 100 | Third-party responses are validated against a schema so unannounced format changes fail loudly. | Find validation at each integration boundary; feed a malformed fixture. | Code lines |  |
| API-07 | T2 | - | A sandbox environment lets customers test their integrations. | Find sandbox docs and keys. | URL |  |
