# Stack file template

A stack file tells the auditor how verification changes for one concrete stack. The checks stay vendor-neutral;
this file holds the commands and settings. Copy it, fill every field, and name it `<stack summary>.md`.
Use placeholders for hostnames and accounts. Never put real hosts, IPs, keys or company names in it.

## Stack

| Layer | Your choice | How this changes verification |
|---|---|---|
| Language and framework | | Which handlers to enumerate (route handlers, server actions, controllers); which validation and sanitizer libraries to look for. |
| Hosting | | How to list listening ports, firewall rules and SSH config (cloud console, CLI, or host commands). |
| Database | | Version command, access rules file, backup tool, archive/PITR setting, pooling. |
| CDN / WAF | | TLS mode to read, how to confirm origin lockdown, cache headers, purge API. |
| Object storage | | Encryption default, lifecycle rule command, public-access setting, signed URL expiry. |
| Email provider | | Sandbox status, DKIM and return-path verification, event routing, suppression list, reputation metrics. |
| Deploy | | Where the pipeline is defined, how to see tests-before-deploy, how rollback works. |
| Monitoring | | Uptime, error tracking, logs, cost alerts, alarms: where each lives. |
| Secrets | | Where secrets live and how to scan them. |

## Commands

List the concrete commands for each check ID that changes on this stack:

| Check IDs | Command or setting | Expected result |
|---|---|---|

## Not applicable on this stack

Checks that are N/A or different here, and why (for example autoscaling on fixed servers).
