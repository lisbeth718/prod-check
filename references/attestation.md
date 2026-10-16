# Owner attestation

Ask the owner these once, at the start. Ask, then look for an artifact (document, log). Artifact you verified: PASS. Owner's word only: ATTESTED, with "owner says: ..." as evidence. Owner admits a gap: FAIL.
If the owner cannot answer, record UNKNOWN. Ask only the questions whose check is in scope (tier, gates). In quick mode ask only the rows whose ID is in `quick-profile.md`.

| ID | Tier | Gate | Question |
|---|---|---|---|
| SEC-03 | T0 | - | Have all keys ever pasted into an AI chat, MCP config or committed file been rotated since? When? |
| AUTHZ-06 | T2 | accounts | When was the last access review, and who signed it off? Is it quarterly? |
| BKP-03 | T0 | - | When did you last restore a backup into a scratch environment and verify the data? Is there a log? |
| BKP-05 | T2 | - | Is there a written disaster recovery plan, and when was it last tested? |
| OBS-09 | T1 | - | Does an alert reach a phone? Can you fire a test alert now and show who gets paged? |
| OBS-10 | T2 | - | When was your last incident, and what changed afterwards? Where is the postmortem? |
| OBS-13 | T2 | - | Who is on call right now, who is the backup, and where are the runbooks? |
| BILL-06 | T1 | payments | Which tax engine or merchant of record is configured, and for which countries? |
| BILL-07 | T1 | usage billing | When did you last reconcile metered usage against invoices, and what was the difference? |
| OPS-06 | T2 | - | Has an external penetration test been done? When, and are findings closed? |
| OPS-07 | T2 | - | Do you keep a security questionnaire answer set with evidence behind each answer? Where? |
| OPS-08 | T2 | - | Does a customer require SOC 2? Status of readiness or audit? |
| OPS-09 | T2 | health data | Is any US patient health data processed? If yes, do you have a BAA with every vendor that touches it, including AI model providers? |
| OPS-10 | T2 | card data | Is PCI scope documented, and which SAQ applies? |
| OPS-11 | T2 | - | Do you have a signed DPA with every vendor that processes customer data (hosting, CDN, email, cloud, AI model providers)? |
| OPS-13 | T0 | - | Who else can deploy and restore without you? Is every credential in a vault or otherwise reachable by a second person? |
| OPS-15 | A | - | Is there a list of single-vendor dependencies (auth, database, hosting, email), what breaks when each is down, and can you export your data from each? Where is it? |
| OPS-17 | T1 | - | Is there a written lost-laptop plan (keys to rotate, sessions to kill, order)? Where? |
