# prod-check

Find the bugs that end startups before your first customer finds them: leaked keys, other users' data, fake payments, no restorable backup.

Run `/prod-check` in your repo. In about 30 minutes your agent checks up to 68 high-risk items with evidence (file:line, command output); anything it cannot reach is reported UNKNOWN. It then hands you the top 5 fixes.

Read-only in production; replays write only to a test environment you provide; test alerts and messages are sent only with your OK. Built for agent-written apps: catches pasted keys, packages that don't exist, IDOR in server actions, Supabase tables open to the browser, and webhooks nobody verifies.

Going enterprise later? `full` covers all checks up to SSO, SOC 2 and DPAs. You don't need those yet.

Based on a checklist by Mari Luukkainen.

## Install

Claude Code:

```sh
git clone https://github.com/lisbeth718/prod-check ~/.claude/skills/prod-check
```

Then run `/prod-check` in your app's repo.

Other agents: give the agent the path to `SKILL.md` and tell it to follow it. It reads the files under `references/` as needed.

Humans: work through `CHECKLIST.md`. Each line has an ID, a tier and a gate. `references/checks/` says how to verify each one.

## Usage

```
/prod-check                      quick audit (default), 68 checks
/prod-check full                 every check in scope, 189 checks
/prod-check quick auth billing   only those sections
/prod-check full 05-database-data-model
```

Quick mode runs and reports only a short list of the highest-risk checks. See `references/quick-profile.md`.
Fixes are proposed after the report and applied only when you say go.

## How it works

### Tiers and gates

A weekend landing page and a multi-tenant billing app do not need the same checks.
Each check has a tier, and some sections are gated on a feature.

- T0 Public site: no accounts, no payments, no customer data.
- T1 Paying: any of: accounts, customer data, payments.
- T2 Enterprise/regulated: SSO, questionnaires, contract terms, or regulated data (health, financial account, legal, member, union or political data). Ordinary PII such as CVs and emails stays T1.
- A (advisory): reported as notes, never a FAIL.

The auditor checks everything at or below your tier. Anything above is N/A, not FAIL.
Gates are features you may not have: accounts, multi-tenant, payments, usage billing, uploads, imports, transactional email,
background jobs, API or webhooks, AI, URL fetching, impersonation, subscriptions, free trials, multiple locales, health data, card data, database, self-hosted servers. A gated-off section is N/A.
`multi-tenant` means an org, team or workspace entity where several users share records; per-user ownership alone is not multi-tenant.

Some things only a person can answer (on-call, postmortems, pen tests, who owns the domain).
The auditor asks you once. An artifact it can verify is PASS, your word alone is ATTESTED, and an admitted gap is FAIL.

### Stages

T0 and T1 rows carry a stage; T2 and advisory rows use `-`. Stages: `launch` (before the first real user outside the team or the first invoice, whichever comes first), `10` (before 10 customers) or `100` (before 100 customers). All T0 rows are `launch`.
Quick mode is the high-risk T0 rows already in the quick list plus every T1 launch row; stages tell you what to fix first, not what is in scope.
Missing IDs were retired and are not reused.

### Review

For T1 and T2 apps a second agent that did not run the audit re-checks every FAIL, every PASS marked "(code only)", plus every PASS in AUTH-*, AUTHZ-*, WEB-01 to WEB-03, BILL-01 and BILL-02. If no reviewer can be spawned, the report says "Reviewer: none" and lists those rows.

### Access

The repo, a shell on the box, read-only database access, and the cloud consoles (hosting, DNS/CDN, email provider, billing).
Anything the auditor cannot reach is UNKNOWN, stated up front in the report header as "Access gaps".

### The agent will see secrets

An agent with this access will read env files, configs and logs. Run it with evidence hygiene on: env vars listed by name only,
no `env`/`printenv`/`docker inspect` dumps, no secret values pasted into the report, redact on sight. Use read-only credentials and a test environment for replays.

### The report

One table: section, ID, check, tier, stage, status (PASS, FAIL, ATTESTED, UNKNOWN, N/A), evidence, fix, effort.
FAILs are ordered by data exposure, data loss, money, availability, then the rest. The top five fixes come last.
See `examples/report-t1-saas.md` (a fictional app) and `references/report-template.md`.

## Add your stack

The checks are vendor-neutral. Commands for a concrete stack live in `references/stacks/`.
Copy `references/stacks/_template.md`, fill it in, and open a PR.
`example-nextjs-postgres-hetzner-cloudflare-ses.md` is a filled-in example.

## Contributing

- One PR per check (or per stack file).
- Every check needs an ID, a tier, a concrete way to verify it, and the evidence to record.
- Keep checks vendor-neutral; put vendor commands in an "e.g." clause or a stack file.
- Process-only checks are marked `attest` and need a question in `references/attestation.md`.
- Keep `CHECKLIST.md` in sync with the check files. No em dashes.

## License

MIT. See `LICENSE`.
