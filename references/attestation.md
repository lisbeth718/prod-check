# Owner attestation

Ask the owner these once, at the start. Answers are recorded as ATTESTED with "owner says: ..." as evidence. ATTESTED is never PASS: nobody verified it.
If the owner cannot answer, record UNKNOWN. Ask only the questions whose check is in scope (tier, gates).

| ID | Tier | Gate | Question |
|---|---|---|---|
| SEC-03 | T0 | - | Have all keys ever pasted into an AI chat, MCP config or committed file been rotated since? When? |
| SEC-07 | T1 | - | Is there a written stolen-laptop plan (keys to rotate, sessions to kill, order)? Where is it? |
| AUTHZ-06 | T2 | accounts | When was the last access review, and who signed it off? Is it quarterly? |
| BKP-03 | T0 | - | When did you last restore a backup into a scratch environment and verify the data? Is there a log? |
| BKP-05 | T2 | - | Is there a written disaster recovery plan, and when was it last tested? |
| OBS-09 | T1 | - | Who is on call right now, who is the backup, and where are the runbooks? |
| OBS-10 | T1 | - | When was your last incident, and what changed afterwards? Where is the postmortem? |
| BILL-07 | T1 | payments | When did you last reconcile metered usage against invoices, and what was the difference? |
| AI-02 | T1 | AI/LLM | Does a DPA (and a BAA for US health data) exist with every AI model provider you send customer data to? |
| OPS-06 | T2 | - | Has an external penetration test been done? When, and are findings closed? |
| OPS-07 | T2 | - | Do you keep a security questionnaire answer set with evidence behind each answer? Where? |
| OPS-08 | T2 | - | Does a customer require SOC 2? Status of readiness or audit? |
| OPS-09 | T2 | health data | Is any US patient health data processed? If yes, do you have a BAA with every vendor that touches it? |
| OPS-10 | T2 | card data | Is PCI scope documented, and which SAQ applies? |
| OPS-11 | T2 | - | Do you have a signed DPA with every vendor that processes customer data (hosting, CDN, email, cloud, AI)? |
| OPS-12 | T0 | - | Are all accounts, domains, repos and the cloud root account in the company's name, with MFA on the root? |
| OPS-13 | T1 | - | Who else can deploy and restore without you? Are credentials in a shared vault? |
