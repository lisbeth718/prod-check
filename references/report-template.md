# Report template

Copy this structure. Fill every field. Keep evidence short; put long output in an appendix.

```
# Ship check report: <app>

- App: <name and repo or URL>
- Tier: T0 | T1 | T2
- Gates on: <accounts, multi-tenant, payments, ...>
- Gates off: <list>
- Mode: quick | full (sections: <list or all>)
- Stack file used: <name or "none, adapted">
- Date: <YYYY-MM-DD>
- Auditor: <agent and model>
- Reviewer: <agent and model, or "n/a (T0)">

## Findings

| Section | ID | Check | Tier | Status | Evidence | Fix | Effort |
|---|---|---|---|---|---|---|---|

## Top 5 fixes

1. <ID>: <fix> (<effort>)
...

## Re-verified by reviewer

<ids, comma separated. Mark any whose status changed: ID (was PASS, now FAIL)>
```

## Column rules

- Status is one of PASS, FAIL, ATTESTED, UNKNOWN, N/A.
- Evidence: `file:line`, command output, config value or test result. For ATTESTED write `owner says: ...`. For N/A write the reason (gate off, above tier).
- Fix: one sentence. Blank for PASS, ATTESTED and N/A.
- Effort: S under half a day, M up to three days, L more than that.

## Row order

1. FAIL rows by severity: data exposure, data loss, money, availability, everything else.
2. UNKNOWN rows.
3. ATTESTED rows.
4. PASS rows, then N/A rows, each in check-file order.

The top 5 fixes use the same severity order, so the next run can diff against this one.
