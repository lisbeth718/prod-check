# Stack: Next.js, self-hosted Postgres, Hetzner, Cloudflare + R2, Amazon SES, push-to-deploy

Worked example of `_template.md`. The author's own stack. Replace names with placeholders when you copy it.
`example.com`, `203.0.113.10` and `app1` are placeholders.

## Stack

| Layer | Choice | How this changes verification |
|---|---|---|
| Framework | Next.js, React, TypeScript | Server actions are public POST endpoints: enumerate `"use server"` files and route handlers. Public env prefix is `NEXT_PUBLIC_`. Validation is usually zod. |
| Hosting | Hetzner Cloud servers (fixed size) | Cloud firewall via `hcloud firewall describe`. Autoscaling is N/A unless load tests demand it. Pick EU locations for EU residency. |
| Database | Self-hosted Postgres, shared by several apps, terminal-only | One instance serves many apps: BKP-04 and DB-06 must cover every database on it. Check `pg_hba.conf` and `listen_addresses`. |
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
| SRV-01 | `grep -vE '^\s*#' /etc/postgresql/*/main/pg_hba.conf` | No `0.0.0.0/0` or `::/0` entries |
| SRV-01 | `hcloud firewall describe <name>` | No inbound rule for 5432 from the internet |
| SRV-01 | `nc -vz 203.0.113.10 5432` from outside | Refused or timeout |
| SRV-02 | Cloudflare dashboard, SSL/TLS: encryption mode | Full (strict), never Flexible |
| SRV-02 | `curl -sI --resolve example.com:443:203.0.113.10 https://example.com` from a non-Cloudflare host | Refused or blocked; origin firewall allows only Cloudflare ranges |
| SRV-03 | `sshd -T \| grep -Ei 'passwordauthentication\|permitrootlogin'` | `no` and `prohibit-password` or `no` |
| SRV-04 | Postgres URL has `sslmode=verify-full`; `SHOW ssl;` | `on` |
| SRV-05 | `pgbackrest info` or WAL-G config | Repo encryption on; R2 encrypted by default |
| SEC-02 | `grep -rE 'NEXT_PUBLIC_' .env* && grep -rE 'AKIA\|sk_live' .next/static` | No secrets in public vars or the bundle |
| AUTH-05 | Limiter key reads `CF-Connecting-IP` | Real client IP, not a Cloudflare range |
| AUTHZ-03 | `SELECT * FROM pg_policies;` | RLS policies on tenant tables, or scoped query helpers |
| DB-17 | `SHOW server_version;` vs postgresql.org versioning policy | Major version still supported |
| BKP-01 | Backup job target is a separate R2 bucket with its own API token | App credentials cannot delete backups |
| BKP-02 | `psql -c 'SHOW archive_mode;'`; `wal-g backup-list` or `pgbackrest info` | `on`, recent WAL |
| BKP-04 | `psql -l` then map each database to a backup job and drill | Every database has both |
| UPL-04 | `aws s3api get-bucket-lifecycle-configuration --bucket <b> --endpoint-url <r2-endpoint>` | Expiry rules for temp prefixes |
| MAIL-01 | `aws sesv2 get-account --region <region>` | `ProductionAccessEnabled: true` |
| MAIL-02 | `aws sesv2 get-email-identity --email-identity example.com`; `dig TXT _dmarc.example.com` | DKIM `SUCCESS`, MAIL FROM `SUCCESS`, DMARC policy moving to quarantine or reject |
| MAIL-03 | `aws sesv2 list-configuration-sets`; check event destinations; `aws sesv2 get-account` | Bounce and complaint events routed; account suppression on |
| MAIL-04 | SES reputation dashboard or `aws sesv2 get-account` reputation fields | Bounce under 5%, complaints under 0.1% |
| MAIL-05 | IAM policy for the sending user | Only `ses:SendEmail` and `ses:SendRawEmail`, limited to verified identities |
| MAIL-06 | `aws cloudwatch describe-alarms` | Alarms for bounce, complaint, send volume |
| JOB-04 | `grep -rn 'pg_try_advisory_lock' .`; `crontab -l` on each server | Each cron job takes a lock |
| OBS-03 | AWS Billing budgets; hosting provider usage alert | Alerts with a same-day recipient |
| OBS-04 | Each AI provider console: usage limits | Limit or alert per key |

## Not applicable or different here

- SRV-08 autoscaling: fixed servers; a standby is only needed if a contract promises more uptime than one box gives.
- DEP-03 and DEP-04: the pipeline is the custom deploy system; hosted CI config will not exist.
