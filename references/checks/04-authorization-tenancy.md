# Authorization and tenancy

Gate: accounts

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| AUTHZ-01 | T1 | Every server action and route handler checks the session and that the record belongs to that user or tenant (no IDOR). | Enumerate handlers; read each ownership check; replay a request as user B with user A's record ID in a test environment. Server actions are public POST endpoints. | Handler list, replay result |  |
| AUTHZ-02 | T1 | Roles and permissions go beyond admin / everyone else and are enforced on the server. | Read the permission model; confirm checks run in handlers, not only in UI hiding. | Code lines |  |
| AUTHZ-03 | T1 | (gate: multi-tenant) Tenant scoping is enforced in the query layer or database row-level security, not only in the UI. | Read the data access layer; list queries without a tenant filter. If RLS is relied on, check it is enabled and applies to the app role: `relrowsecurity` and `relforcerowsecurity` per table, plus the role attributes, because table owners, superusers and BYPASSRLS roles skip RLS. e.g. `SELECT relname, relrowsecurity, relforcerowsecurity FROM pg_class WHERE relkind = 'r';`; `SELECT rolname, rolsuper, rolbypassrls FROM pg_roles;`; `SELECT * FROM pg_policies;` alone is not enough. | Query list, RLS flags, role attributes, policy output |  |
| AUTHZ-04 | T1 | Admin "log in as user" for support exists and every use is written to an audit log. | Find impersonation code; check it logs actor, target, time, reason; check it is permission-gated. | Code lines, sample log row |  |
| AUTHZ-05 | T2 | An audit trail records who viewed a record, not only who changed it. | Find read-access logging for sensitive records and sample a row. | Code lines, sample row |  |
| AUTHZ-06 | T2 | Quarterly access reviews are signed off by a named person. | Ask the owner for the last review record. | Last review date, signer | attest |
