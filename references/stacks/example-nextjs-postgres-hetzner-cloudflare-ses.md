# Stack: Next.js, self-hosted Postgres, Hetzner, Cloudflare + R2, Amazon SES, push-to-deploy

Worked example of `_template.md`. Replace names with placeholders when you copy it.
`example.com`, `203.0.113.10` and `app1` are placeholders.

## Stack

| Layer | Choice | How this changes verification |
|---|---|---|
| Framework | Next.js, React, TypeScript | Server actions are public POST endpoints: enumerate `"use server"` files and route handlers. Public env prefix is `NEXT_PUBLIC_`. Validation is usually zod. |
| Hosting | Hetzner Cloud servers (fixed size) | Cloud firewall via `hcloud firewall describe`. Autoscaling is N/A unless load tests demand it. Pick EU locations for EU residency. |
| Database | Self-hosted Postgres, shared by several apps, terminal-only | One instance serves many apps: BKP-04 and DB-06 must cover every database on it. With one database container per app, map each container to its own backup job and drill (BKP-01, BKP-04). Check `pg_hba.conf` and `listen_addresses`; in Docker read it with `docker exec <db> cat $PGDATA/pg_hba.conf`. |
| Reverse proxy | Traefik or nginx in front of the app | Real client IP needs `forwardedHeaders.trustedIPs` (Traefik) or `set_real_ip_from` (nginx) set to the Cloudflare ranges, else every request looks like the proxy and rate limiting collapses to one bucket. |
| Auth library | e.g. Better Auth | Check the library's defaults for token hashing (e.g. `storeIdentifier`, `storeToken`) and grep app logs for limiter or IP warnings. |
| CDN / WAF | Cloudflare proxy | Read SSL mode; allowlist Cloudflare ranges at the origin; rate limit on `CF-Connecting-IP`, not the proxy IP. |
| Object storage | Cloudflare R2 | Private buckets, presigned URLs, lifecycle rules, EU jurisdiction. Encrypted by default. |
| Email | Amazon SES | Sandbox, Easy DKIM, custom MAIL FROM, configuration set, suppression list, CloudWatch alarms. |
| Deploy | Push to the default branch; a custom deploy system builds on the server | Tests-before-prod and rollback live in the deploy system, not in a hosted CI config. Read its config and last rollback record. |
| Monitoring | External uptime probe, error tracker, cron-based host watchers | Cost alerts: AI provider consoles, AWS billing alarm, hosting provider usage alert. |
| Secrets | Env files on the server, not in git | Run gitleaks over history; check file modes on the server. |

## Commands

| Check IDs | Command or setting | Expected result |
|---|---|---|
| SRV-01 | `ss -tlnp \| grep 5432` | Bound to `127.0.0.1` or a private address only |
| SRV-01 | `grep -vE '^\s*#' /etc/postgresql/*/main/pg_hba.conf` | No `0.0.0.0/0`, `::/0`, `all` or `samenet` address on remote rules |
| SRV-01 | `grep -n -A3 'ports:' docker-compose*.yml compose*.yml`; stack files likewise | No bare `5432:5432` (binds all interfaces and bypasses ufw); use `127.0.0.1:5432:5432` or no published port |
| SRV-01 | `docker exec <db> cat $PGDATA/pg_hba.conf` | Container DB with no published port, reachable only on a docker network, PASSES even with `host all all all` |
| SRV-01 | `hcloud firewall describe <name>` | No inbound rule for 5432 from the internet |
| SRV-01 | `nc -vz 203.0.113.10 5432` from outside | Refused or timeout |
| SRV-02 | Cloudflare dashboard, SSL/TLS: encryption mode | Full (strict), never Flexible |
| SRV-02 | `curl -sI --resolve example.com:443:203.0.113.10 https://example.com` from a non-Cloudflare host | Refused or blocked; origin firewall allows only Cloudflare ranges |
| SRV-03 | `sshd -T \| grep -Ei 'passwordauthentication\|permitrootlogin'` | `no` and `prohibit-password` or `no` |
| SRV-04 | Postgres URL has `sslmode=verify-full`; `SHOW ssl;` | `on` |
| BKP-01 | `pgbackrest info` or WAL-G config | Repo encryption on; R2 encrypted by default |
| SRV-09 | `journalctl --disk-usage`; compose `logging:` `max-size`; `docker system df`; prune timer | Capped logs, scheduled prune, disk alert near 80% |
| SRV-10 | `timedatectl`; compose `mem_limit` and `restart:` | UTC, NTP synced, limits set |
| SEC-08 | `cut -d= -f1 .env.production`; key mode prefix only | No test keys in production, no live keys in dev |
| SEC-02 | `grep -rE 'NEXT_PUBLIC_' .env*; grep -rE 'AKIA\|sk_live' .next/static` | No secrets in public vars or the bundle |
| AUTH-05, WEB-10 | Limiter key reads `CF-Connecting-IP`; Traefik `forwardedHeaders.trustedIPs` or nginx `set_real_ip_from` lists Cloudflare ranges | Real client IP, not a Cloudflare range or one shared bucket |
| AUTH-05 | `docker logs <app> 2>&1 \| grep -i 'rate\|limit\|ip'` | No limiter or missing-IP warnings |
| AUTHZ-03 | `SELECT relname, relrowsecurity, relforcerowsecurity FROM pg_class WHERE relkind='r';` plus `SELECT rolname, rolsuper, rolbypassrls FROM pg_roles;` | RLS enabled on tenant tables and the app role is not owner, superuser or BYPASSRLS; or scoped query helpers |
| DB-18 | `\du`; `SELECT tablename, tableowner FROM pg_tables;` | App role is not superuser or owner |
| DB-17 | `SHOW server_version;` vs postgresql.org versioning policy | Major version still supported |
| BKP-01 | Backup job target is a separate R2 bucket with its own API token | App credentials cannot delete backups |
| BKP-02 | `psql -c 'SHOW archive_mode;'`; `wal-g backup-list` or `pgbackrest info` | `on`, recent WAL |
| BKP-04 | `psql -l` then map each database to a backup job and drill | Every database has both |
| UPL-04 | `aws s3api get-bucket-lifecycle-configuration --bucket <b> --endpoint-url <r2-endpoint>` | Expiry rules for temp prefixes |
| MAIL-02 | `aws sesv2 get-account --region <region>` | `ProductionAccessEnabled: true` |
| MAIL-02 | `aws sesv2 get-email-identity --email-identity example.com`; `dig TXT _dmarc.example.com` | DKIM `SUCCESS`, MAIL FROM `SUCCESS`, DMARC policy moving to quarantine or reject |
| MAIL-03 | `aws sesv2 list-configuration-sets`; check event destinations; `aws sesv2 get-account` | Bounce and complaint events routed; account suppression on |
| MAIL-02 | CloudWatch `Reputation.BounceRate` and `Reputation.ComplaintRate` (or the SES console reputation dashboard) | Bounce under 5%, complaints under 0.1% |
| MAIL-05 | IAM policy for the sending user | Only `ses:SendEmail` and `ses:SendRawEmail`, limited to verified identities |
| MAIL-06 | `aws cloudwatch describe-alarms` | Alarms for bounce, complaint, send volume |
| JOB-04 | `grep -rn 'pg_try_advisory_lock' .`; `crontab -l` on each server | Each cron job takes a lock |
| OBS-03 | AWS Billing budgets; hosting provider usage alert | Alerts with a same-day recipient |
| OBS-04 | Each AI provider console: usage limits | Limit or alert per key |

## Not applicable or different here

- Next.js middleware-only auth was bypassable (CVE-2025-29927). Require Next >= 15.2.3 (or 14.2.25 on the 14 line) and check auth in route handlers and server actions too, not only in middleware.
- SRV-08 autoscaling: fixed servers; a standby is only needed if a contract promises more uptime than one box gives.
- DEP-03 and DEP-04: the pipeline is the custom deploy system; hosted CI config will not exist.
