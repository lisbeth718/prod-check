# ship-check

A production-readiness audit that an AI agent can run on your app, or you can run by hand.
It checks 158 things across 19 sections, from open database ports to restore drills to refund logic.
Every finding needs evidence. The audit is read-only and ends with a report and the top five fixes.

Based on a checklist by Mari Luukkainen.

## Tiers and gates

A weekend landing page and a multi-tenant billing app do not need the same checks.
Each check has a tier, and some sections are gated on a feature.

- T0: no accounts, no payments, no customer data.
- T1: any of those.
- T2: enterprise or regulated (SSO, questionnaires, health or financial data, contract terms).

The auditor checks everything at or below your tier. Anything above is N/A, not FAIL.
Gates are features you may not have: accounts, multi-tenant, payments, uploads, transactional email,
background jobs, API or webhooks, AI, multiple locales, health data, card data. A gated-off section is N/A.

Some things only a person can answer (on-call, postmortems, pen tests, who owns the domain).
The auditor asks you once, records ATTESTED, and never counts it as PASS.

## Install

Claude Code:

```sh
git clone https://github.com/<you>/ship-check ~/.claude/skills/ship-check
```

Then run `/ship-check` in your app's repo.

Other agents: give the agent the path to `SKILL.md` and tell it to follow it. It reads the files under `references/` as needed.

Humans: work through `CHECKLIST.md`. Each line has an ID, a tier and a gate. `references/checks/` says how to verify each one.

## Usage

```
/ship-check                      quick audit (default), about 60 to 90 minutes of agent time
/ship-check full                 every check in scope, about half a day
/ship-check quick auth billing   only those sections
/ship-check full 05-database-data-model
```

Quick mode runs all T0 checks plus a short list of the highest-risk T1 checks. See `references/quick-profile.md`.
For T1 and T2 apps a second agent that did not run the audit re-checks every FAIL and every authorization PASS.
Fixes are proposed after the report and applied only when you say go.

## What the report looks like

One table: section, ID, check, tier, status (PASS, FAIL, ATTESTED, UNKNOWN, N/A), evidence, fix, effort.
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
