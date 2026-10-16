# Database and data model

Gate: none for the file. DB-17 and DB-18 are gated per row on database.

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| DB-01 | T1 | The schema was designed: normalized where it should be, constraints and foreign keys present, no obviously ad hoc tables. | Read the schema; list tables without primary keys or foreign keys; flag tables added by agents without design. e.g. `\d+` in psql, schema dump. | Schema excerpts, flagged tables |  |
| DB-02 | T1 | The data model survives the second customer's "one small change": settings or configurable fields instead of hardcoded single-customer logic. | Grep for customer names, IDs or hardcoded plan logic in code; read how per-customer variation is stored. | grep output, code lines |  |
| DB-03 | T1 | Migrations are versioned and reversible, tested in both directions. | List migrations; confirm each has a down step; run up, down, up on a scratch database. | Migration list, run output |  |
| DB-04 | T1 | Migrations take no long table locks. | Read recent migrations for blocking patterns; require the safe forms. e.g. `CREATE INDEX CONCURRENTLY`; add nullable column, backfill, then constrain. | Migration lines |  |
| DB-05 | T1 | Indexes exist on foreign keys and filtered or sorted columns, checked against production-sized data. | List FKs without indexes; review top queries with plans. e.g. `pg_stat_statements`, `EXPLAIN ANALYZE` on a production-sized copy. | Query list, plan output |  |
| DB-06 | T1 | Connection pooling is in place with headroom under the DB connection limit across every app sharing the instance. | Read pool config; sum pool sizes across apps; compare with the limit. e.g. PgBouncer, `SHOW max_connections;`. | Config values, sum |  |
| DB-07 | T1 | Pagination stays fast at depth (keyset or cursor, not large OFFSET). | Grep list endpoints for OFFSET; time page 400 on realistic data. | Code lines, timing |  |
| DB-08 | T1 | Money is stored as integer minor units; never as floating point. | Check money column types and arithmetic in code. Flag `real`, `double precision` and `float` columns and JS float math on amounts. Fixed-scale `numeric` is acceptable; integer minor units are preferred. e.g. grep `float\|double\|real` on price columns. | Schema lines, code lines |  |
| DB-10 | T1 | Timestamps are stored as timezone-aware types; user-facing schedules are computed in the user's timezone. | Check column types; test a schedule across DST for a user in another zone. e.g. Postgres `timestamptz`. | Schema lines, test result |  |
| DB-12 | T1 | Retention rules from contracts and the privacy policy are implemented as code that deletes on schedule. | Compare stated retention with scheduled deletion jobs; check a job ran recently. | Policy text, job code, last run |  |
| DB-13 | T1 | GDPR deletion reaches every table, object storage, logs and the email suppression list where relevant, with a stated backup expiry. | List all stores of personal data; read the deletion routine; run it for a test user and re-query. | Store list, test result |  |
| DB-14 | T1 | Customers can export their data (CSV or XLSX). | Find the export feature; export a large account and open the file. | Feature location, test result |  |
| DB-15 | T1 | Imports survive real-world CSVs: BOMs, Windows-1252, semicolon delimiters, Excel date formats, quoted newlines. | Run fixture files with each quirk through the importer. | Fixture results |  |
| DB-16 | T2 | Data residency matches contracts: hosting location, object storage jurisdiction, email region and model provider region. | List every data location; compare with contract terms. e.g. EU-only means EU regions for compute, storage, email and AI. | Location table, contract clause |  |
| DB-17 | T0 | (gate: database) The database major version is still within upstream security support. | Read the server version and the vendor's support calendar. e.g. `SHOW server_version;` against postgresql.org versioning policy. | Version, end-of-support date |  |
| DB-18 | T1 | (gate: database) The application's database role is not a superuser or the table owner, has no BYPASSRLS, and holds least-privilege grants. | List roles and attributes, table owners and the grants of the role in the connection string. Migrations should run under a separate owner role. e.g. `\du`; `SELECT rolname, rolsuper, rolbypassrls FROM pg_roles;`; `SELECT tablename, tableowner FROM pg_tables WHERE schemaname = 'public';`; `\dp`. | Role attributes, owner list, grant list |  |

<!-- Retired IDs: DB-09 (merged into BILL-06), DB-11 (merged into UX-04). Numbers are not reused. -->
