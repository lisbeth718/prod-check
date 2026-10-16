# Operations, compliance and ownership

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| OPS-01 | T1 | An admin panel covers ops tasks that would otherwise need direct DB access. | Find the panel and list its actions; list common ops tasks still done in SQL. | Action list |  |
| OPS-02 | T1 | Support tooling lets customer success answer questions without pinging engineering. | Find support views and impersonation or lookup tools. | Tool list |  |
| OPS-03 | T1 | A customer-facing changelog exists. | Find the changelog page and its last entry date. | URL, date |  |
| OPS-04 | T0 | Terms and privacy policy match what the code actually does. | Compare policy claims (data collected, processors, retention, cookies) with code and network calls. | Claim-to-code table |  |
| OPS-05 | T1 | A security.txt and vulnerability disclosure process exist. | Fetch `/.well-known/security.txt`; check the contact works. | Fetched file |  |
| OPS-06 | T2 | A penetration test happened before the customer's security team runs their own. | Ask for the latest report summary and date. | Report date, vendor | attest |
| OPS-07 | T2 | Security questionnaire answers are backed by an evidence folder. | Ask to see the folder and sample answers against evidence. | Folder location | attest |
| OPS-08 | T2 | SOC 2 is in place if the sales motion requires it. | Ask for report or readiness status. | Report date or plan | attest |
| OPS-09 | T2 | (gate: health data) HIPAA controls are in place and a BAA is signed with every vendor touching the data, including the model provider. | Ask for the vendor list and BAAs. | BAA list | attest |
| OPS-10 | T2 | (gate: card data) PCI scope is documented. | Ask for the scope document and the SAQ type. | Document | attest |
| OPS-11 | T2 | DPAs are signed with hosting, CDN, email, cloud and every model provider. | Ask for the vendor list and DPAs. | DPA list | attest |
| OPS-12 | T0 | Every account, domain, repo and cloud account (including cloud root, with MFA) is owned by the company, not a person. | Check registrar, repo org, cloud root and billing owner records. | Owner records | attest |
| OPS-13 | T1 | Bus factor above one: credentials are in a shared vault and at least one other person can deploy and restore. | Ask who else can deploy and restore; check the vault. | Names | attest |
