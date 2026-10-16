# Authentication

Gate: accounts

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| AUTH-01 | T1 | Email normalization handles case and plus-addressing; login works for `name+tag@domain`. | Read the signup and login normalization code; test signup and login with `Name+tag@Example.com` against a test environment. | Code lines, test result |  |
| AUTH-02 | T1 | Reset tokens are hashed in the DB, single-use and expiring. | Read token creation and redemption code and the schema; reuse a token twice and one past expiry in a test environment. | Code lines, test result |  |
| AUTH-03 | T1 | MFA is available with a recovery path for a lost phone. | Find MFA enrollment and recovery code flows (recovery codes, support-assisted reset with identity checks). | Code lines, flow screenshot or test |  |
| AUTH-04 | T1 | Sessions time out, and all sessions for one user can be killed at once. | Read session store and expiry config; find the kill-all path (session version or delete-by-user) and test it. | Config value, code lines, test result |  |
| AUTH-05 | T1 | Login and reset are rate-limited on the real client IP, not the proxy's IP. | Read the limiter key; confirm it uses the trusted forwarded header; send repeated attempts in a test environment. e.g. behind Cloudflare use `CF-Connecting-IP`. | Code lines, status codes after N attempts |  |
| AUTH-06 | T1 | Lockouts slow attackers (progressive delay, per account and per IP) without hard-locking real users, and an admin unlock path exists. | Read lockout logic; check for a hard permanent lock; find the unlock path. | Code lines |  |
| AUTH-07 | T2 | SSO (SAML and OIDC) is supported for customers who require it. | Find SSO config, metadata handling, and test with an IdP sandbox. | Code lines, test result |  |
| AUTH-08 | T2 | SCIM provisioning and deprovisioning removes access immediately when IT offboards someone. | Find SCIM endpoints; deactivate a test user and confirm sessions and tokens die. | Code lines, test result |  |
