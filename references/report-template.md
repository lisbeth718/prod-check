# Report template

Copy this structure. Fill every field. Keep evidence short; put long output in an appendix.

```
# Ship check report: <app>

- App: <name and repo or URL>
- Tier: T0 | T1 | T2
- Gates on: <accounts, multi-tenant, payments, database, self-hosted servers, ...>
- Gates off: <list>
- Mode: quick | full (sections: <list or all>)
- Stack file used: <name or "none, adapted">
- Date: <YYYY-MM-DD>
- Auditor: <agent and model>
- Reviewer: <agent and model, "n/a (T0)", or "none">

(Quick mode only, keep this line:) A quick run proves the worst failures are absent. It does not prove readiness.

## Findings

| Section | ID | Check | Tier | Status | Evidence | Fix | Effort |
|---|---|---|---|---|---|---|---|

## Top 5 fixes

1. <ID>: <fix> (<effort>)
...

## Re-verified by reviewer

<ids, comma separated. Mark any whose status changed: ID (was PASS, now FAIL)>
<If "Reviewer: none": write "Reviewer: none" and list the rows that would have been re-verified.>
```

## Column rules

- Status is one of PASS, FAIL, ATTESTED, UNKNOWN, N/A.
  - PASS: you verified an artifact or evidence yourself.
  - FAIL: the evidence shows it fails, or the owner admits the gap.
  - ATTESTED: the owner's word only, no artifact to verify.
  - UNKNOWN: you could not verify it and got no answer.
  - N/A: above tier, gate off, or "not applicable to this architecture (state why)".
- Evidence: `file:line`, command output, config value or test result. For ATTESTED write `owner says: ...`. For N/A write the reason.
- Fix: one sentence. Blank for PASS, ATTESTED and N/A.
- Effort: S under half a day, M up to three days, L more than that.

## Row order

1. FAIL rows by severity: data exposure, data loss, money, availability, everything else.
2. UNKNOWN rows.
3. ATTESTED rows.
4. PASS rows, then N/A rows, each in check-file order.

The top 5 fixes use the same severity order, so the next run can diff against this one.

## Reviewer scope (T1 and T2)

The reviewer re-checks every FAIL plus every PASS in AUTH-*, AUTHZ-*, WEB-01 to WEB-03, BILL-01 and BILL-02.
