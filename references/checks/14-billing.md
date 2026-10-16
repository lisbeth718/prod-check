# Billing

Gate: payments. BILL-07 is also gated on usage billing and BILL-16 on subscriptions.

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| BILL-01 | T1 | invoice | Incoming payment webhook signatures are verified. | Read webhook handler; send an unsigned or tampered request in a test environment. | Code lines, test result |  |
| BILL-02 | T1 | invoice | Idempotency keys are used on every direct charge or refund call; with hosted checkout, refund calls carry keys and event dedupe is verified under BILL-12. | Grep charge and refund calls for an idempotency key derived from the order. With hosted checkout there may be no direct charge call: verify refund keys and BILL-12. | Code lines |  |
| BILL-03 | T1 | 10 | Subscriptions, upgrades, downgrades and proration are correct. | Run test scenarios for each change and compare invoice math. | Scenario results |  |
| BILL-04 | T1 | 10 | Failed payments are retried and dunning emails are sent. | Read retry settings; simulate a failed card. | Config, test result |  |
| BILL-05 | T2 | - | Refunds, credits and partial refunds work. | Issue each in a test environment; check ledger and invoice. | Test result |  |
| BILL-06 | T1 | invoice | A tax engine or merchant of record is configured for every country sold into (VAT, sales tax, EU reverse charge for B2B). | Ask which tax setup is used; read its configuration and the sold-into country list; open a B2C and a B2B sample invoice. | Configuration, country list, invoice samples | attest |
| BILL-07 | T1 | 100 | (gate: usage billing) Usage metering matches what is billed, to the cent, and is reconciled at least monthly. | Recompute a sample account from raw usage; ask for the last reconciliation. | Recompute table | attest |
| BILL-08 | T1 | invoice | Card numbers never touch your database or logs. | Check the card entry method and grep DB schema and logs for PAN patterns. e.g. hosted fields from the processor. | Code lines, grep output |  |
| BILL-09 | T2 | - | Invoices carry the customer's PO number. | Generate an invoice with a PO set. | Invoice sample |  |
| BILL-10 | T2 | - | Annual contracts with custom terms are supported without code forks. | Find where contract terms are stored; check no per-customer branches. | Code lines |  |
| BILL-11 | T2 | - | Revenue reports reconcile against the payment processor. | Compare a month of report totals to processor payouts. | Reconciliation table |  |
| BILL-12 | T1 | invoice | Payment webhooks are deduplicated on event id and out-of-order events are handled: a later subscription update is never overwritten by an earlier retry. | Find the event-id table or unique index; read the handler for idempotency and ordering; replay one event twice and deliver an older event after a newer one in a test environment. | Schema lines, code lines, replay result |  |
| BILL-13 | T1 | invoice | Paid access is derived from provider state (webhook plus periodic reconciliation), never from the checkout success redirect. | Grep the success-URL handler for entitlement writes; find the reconciliation job. | grep output, code lines |  |
| BILL-14 | T1 | invoice | Card-testing protection: fraud rules, or a rate limit plus bot challenge, on payment-intent, setup-intent and checkout-session creation. | Find the endpoints that create payment objects; confirm a rate limit and bot challenge; check provider fraud rules are on. e.g. Stripe Radar rules. | Code lines, rule list |  |
| BILL-15 | T1 | invoice | Dispute and chargeback events alert a human, and the dispute rate is watched (card networks and the provider can close accounts at about 1%). | Find the dispute webhook handler and its alert target; read the current dispute rate in the provider dashboard. | Handler lines, alert target, dispute rate |  |
| BILL-16 | T1 | invoice | (gate: subscriptions) Customers can cancel in the app without contacting support (auto-renewal laws). | Find the cancel route or customer-portal link; follow it in a test environment and confirm renewal stops. | Route or link, test result |  |
