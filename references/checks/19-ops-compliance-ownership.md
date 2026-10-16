# Operations, compliance and ownership

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| OPS-01 | T2 | An admin panel covers ops tasks that would otherwise need direct DB access. | Find the panel and list its actions; list common ops tasks still done in SQL. | Action list |  |
| OPS-02 | T2 | Support tooling lets customer success answer questions without pinging engineering. | Find support views and impersonation or lookup tools. | Tool list |  |
| OPS-03 | T2 | A customer-facing changelog exists. | Find the changelog page and its last entry date. | URL, date |  |
| OPS-04 | T0 | Terms and privacy policy match what the code actually does. | Compare policy claims (data collected, processors, retention, cookies) with code and network calls. | Claim-to-code table |  |
| OPS-05 | T1 | A security.txt and vulnerability disclosure process exist. | Fetch `/.well-known/security.txt`; check the contact works and `Expires` is not in the past (RFC 9116). | Fetched file |  |
| OPS-06 | T2 | A penetration test happened before the customer's security team runs their own. | Ask for the latest report summary and date. | Report date, vendor | attest |
| OPS-07 | T2 | Security questionnaire answers are backed by an evidence folder. | Ask to see the folder and sample answers against evidence. | Folder location | attest |
| OPS-08 | T2 | SOC 2 is in place if the sales motion requires it. | Ask for report or readiness status. | Report date or plan | attest |
| OPS-09 | T2 | (gate: health data) HIPAA controls are in place and a BAA is signed with every vendor touching the data, including the model provider. | Ask for the vendor list and BAAs. | BAA list | attest |
| OPS-10 | T2 | (gate: card data) PCI scope is documented. | Ask for the scope document and the SAQ type. | Document | attest |
| OPS-11 | T2 | DPAs are signed with hosting, CDN, email, cloud and every model provider. | Ask for the vendor list and DPAs. | DPA list | attest |
| OPS-12 | T0 | Every account, domain, repo and cloud account (including cloud root, with MFA) is owned by the company, not a person. | Check the registrar registrant and account owner, repo org owners list, cloud organization owner and billing contact, and root MFA status. The owner admitting a personal-name account is a FAIL. e.g. `whois example.com`; cloud account summary shows root MFA enabled. | Owner records, MFA status |  |
| OPS-13 | T0 | Credential escrow: every credential is reachable by a second person or a vault, at least one other person can deploy and restore, and a written lost-laptop plan lists keys to rotate, sessions to kill and the order. | Ask who else can deploy and restore; list vault entries by name (never values); read the lost-laptop plan. | Names, vault entry list, plan location and date | attest |
| OPS-14 | T0 | Third-party scripts are listed (analytics, chat, replay); cookie consent is in place where required; no personal data goes to analytics; session replay masks inputs. | List script tags and third-party requests on a fresh page load; read the consent logic; read analytics event payloads and the replay masking config. | Script list, consent behavior, config lines |  |
| OPS-15 | T1 | A single-vendor dependency list exists (auth, database, hosting, email): what breaks when each is down, and confirmation that data can be exported from each. | Ask for the list and look for a document; check that an export path exists for each vendor. | List location, export confirmation | attest |
