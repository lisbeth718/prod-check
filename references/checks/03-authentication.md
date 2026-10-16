# Authentication

Gate: accounts

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| AUTH-01 | T1 | Emails are normalized by lowercasing and trimming, the `+tag` is kept, and uniqueness is enforced by a unique index on `lower(email)` or a case-insensitive type; login works for `name+tag@domain`. | Read the signup and login normalization code and the unique index; confirm tags are never stripped; test signup and login with `Name+tag@Example.com` against a test environment. | Code lines, test result |  |
| AUTH-02 | T1 | Reset tokens are hashed in the DB, single-use and expiring. | Read token creation and redemption code and the schema; reuse a token twice and one past expiry in a test environment. | Code lines, test result |  |
| AUTH-03 | T1 | MFA is available with a recovery path for a lost phone. | Find MFA enrollment and recovery code flows (recovery codes, support-assisted reset with identity checks). | Code lines, flow screenshot or test |  |
| AUTH-04 | T1 | Sessions time out, and all sessions for one user can be killed at once. | Read session store and expiry config; find the kill-all path (session version or delete-by-user) and test it. | Config value, code lines, test result |  |
| AUTH-05 | T1 | Login and reset are rate-limited on the real client IP, not the proxy's IP. | Read the limiter key; confirm it uses the trusted forwarded header; send repeated attempts in a test environment. e.g. behind Cloudflare use `CF-Connecting-IP`. | Code lines, status codes after N attempts |  |
| AUTH-06 | T1 | Lockouts slow attackers (progressive delay, per account and per IP) without hard-locking real users, and an admin unlock path exists. | Read lockout logic; check for a hard permanent lock; find the unlock path. | Code lines |  |
| AUTH-07 | T2 | SSO (SAML and OIDC) is supported for customers who require it. | Find SSO config, metadata handling, and test with an IdP sandbox. | Code lines, test result |  |
| AUTH-08 | T2 | SCIM provisioning and deprovisioning removes access immediately when IT offboards someone. | Find SCIM endpoints; deactivate a test user and confirm sessions and tokens die. | Code lines, test result |  |
| AUTH-09 | T1 | Passwords are hashed with a password hash (argon2id, bcrypt or scrypt) at a sane cost. | Read the hashing code and its cost parameters; read the hash prefix of one DB row (prefix only, never the full hash); grep for fast hashes used on passwords. e.g. `$argon2id$`, `$2b$`, `$scrypt$`; `grep -rEn 'md5\|sha1\|sha256' src \| grep -i pass`. N/A with reason if there are no passwords (SSO or passwordless only). | Code lines, cost values, hash prefix |  |
| AUTH-10 | T1 | Session cookies set HttpOnly, Secure and SameSite=Lax or Strict. | Log in on a test environment and read the `Set-Cookie` headers. e.g. `curl -si -X POST https://host/login -d '...' \| grep -i set-cookie`. | Response headers |  |
| AUTH-11 | T1 | Login, password reset and signup do not reveal whether an account exists: same response and similar timing for existing and missing accounts. | Send requests for an existing and a missing address to each flow in a test environment; compare status, body and response time over several runs. | Response diff, timings |  |
