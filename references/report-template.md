# Report template

Copy this structure. Fill every field. Keep evidence short; put long output in an appendix.

```
# Prod check report: <app>

- App: <name and repo or URL>
- Tier: T0 Public site | T1 Paying | T2 Enterprise/regulated
- Gates on: <accounts, multi-tenant, payments, database, self-hosted servers, ...>
- Gates off: <list>
- Mode: quick | full (sections: <list or all>)
- Stack file used: <name or "none, adapted">
- Date: <YYYY-MM-DD>
- Auditor: <agent and model>
- Reviewer: <agent and model, "n/a (T0)", or "none">
- Access gaps: <what you could not reach (repo, shell, DB, hosting, DNS/CDN, email, billing), or "none">

(Quick mode only, keep this line:) A quick run proves the worst failures are absent. It does not prove readiness.

## Findings

| Section | ID | Check | Tier | Stage | Status | Evidence | Fix | Effort |
|---|---|---|---|---|---|---|---|---|

## Advisory notes

<Tier A rows, one line each. Never FAIL. Full mode only. Omit in quick mode.>

## Top 5 fixes

1. <ID>: <fix> (<effort>)
...

## Re-verified by reviewer

<ids, comma separated. Mark any whose status changed: ID (was PASS, now FAIL)>
<If "Reviewer: none": write "Reviewer: none" and list the rows that would have been re-verified.>
```

## Column rules

- Stage is `launch`, `10`, `100` or `-`, copied from the check file. It says when the fix is due, not whether the row is in scope.
- Status is one of PASS, FAIL, ATTESTED, UNKNOWN, N/A.
  - PASS: you verified an artifact or evidence yourself. If a live replay was needed and only code was read, write PASS and add "(code only)" to the evidence.
  - FAIL: the evidence shows it fails, or the owner admits the gap. A row with one FAIL sub-item is FAIL; note any UNKNOWN part in the evidence.
  - ATTESTED: the owner's word only, no artifact to verify.
  - UNKNOWN: you could not verify it and got no answer.
  - N/A: above tier, gate off, or "not applicable to this architecture (state why)".
- Evidence: `file:line`, command output, config value or test result. Env vars by name only; never paste secret values or `env`/`docker inspect` dumps. For ATTESTED write `owner says: ...`. For N/A write the reason.
- Fix: one sentence. Blank for PASS, ATTESTED and N/A.
- Effort: S under half a day, M up to three days, L more than that.

## Row order

1. FAIL rows by severity: data exposure, data loss, money, availability, everything else.
2. UNKNOWN rows.
3. ATTESTED rows.
4. PASS rows, then N/A rows, each in check-file order.

The top 5 fixes use the same severity order, so the next run can diff against this one.

## Reviewer scope (T1 and T2)

The reviewer re-checks every FAIL, every PASS marked "(code only)", plus every PASS in AUTH-*, AUTHZ-*, WEB-01 to WEB-03, BILL-01 and BILL-02.
