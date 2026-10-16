# Billing

Gate: payments

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| BILL-01 | T1 | Incoming payment webhook signatures are verified. | Read webhook handler; send an unsigned or tampered request in a test environment. | Code lines, test result |  |
| BILL-02 | T1 | Idempotency keys are used on every charge. | Grep charge calls for an idempotency key derived from the order. | Code lines |  |
| BILL-03 | T1 | Subscriptions, upgrades, downgrades and proration are correct. | Run test scenarios for each change and compare invoice math. | Scenario results |  |
| BILL-04 | T1 | Failed payments are retried and dunning emails are sent. | Read retry settings; simulate a failed card. | Config, test result |  |
| BILL-05 | T1 | Refunds, credits and partial refunds work. | Issue each in a test environment; check ledger and invoice. | Test result |  |
| BILL-06 | T1 | Currency, rounding, VAT and sales tax are correct for every country sold into, including EU reverse charge for B2B. | List sold-into countries; test sample amounts per currency and rounding mode; test invoices for B2C and B2B in several countries; check tax ID validation. | Test table, country list, invoice samples |  |
| BILL-07 | T1 | Usage metering matches what is billed, to the cent, and is reconciled at least monthly. | Recompute a sample account from raw usage; ask for the last reconciliation. | Recompute table | attest |
| BILL-08 | T1 | Card numbers never touch your database or logs. | Check the card entry method and grep DB schema and logs for PAN patterns. e.g. hosted fields from the processor. | Code lines, grep output |  |
| BILL-09 | T2 | Invoices carry the customer's PO number. | Generate an invoice with a PO set. | Invoice sample |  |
| BILL-10 | T2 | Annual contracts with custom terms are supported without code forks. | Find where contract terms are stored; check no per-customer branches. | Code lines |  |
| BILL-11 | T2 | Revenue reports reconcile against the payment processor. | Compare a month of report totals to processor payouts. | Reconciliation table |  |
