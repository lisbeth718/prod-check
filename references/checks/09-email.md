# Email

Gate: transactional email

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| MAIL-02 | T0 | - | Per sending domain: production sending access (not a sandbox or trial), DKIM verified, a custom return-path (MAIL FROM) domain so SPF aligns, DMARC with quarantine or reject, and, once there are 30 days of sending, bounce rate under 5% and complaint rate under 0.1% (N/A under 30 days of sending). `p=none` is FAIL at T1 and PASS with a note at T0 if `rua` is set. | Read provider account status, verification status and reputation metrics; query DNS. e.g. `dig TXT _dmarc.example.com`; `aws sesv2 get-account` shows ProductionAccessEnabled; `aws sesv2 get-email-identity`; the SES reputation dashboard. | DNS output, account and identity status, metric values |  |
| MAIL-03 | T0 | - | Every send uses a tracking configuration; bounce and complaint events reach a handler that stops mailing those addresses; account-level suppression is on. | Find the send call and event routing; check the handler suppresses addresses. e.g. SES configuration set, SNS or EventBridge destination, account suppression list. | Code lines, config |  |
| MAIL-05 | T0 | - | Email sending credentials are scoped to sending only, restricted to verified identities, and used by nothing else. | Read the credential's policy and where it is used. | Policy document |  |
| MAIL-06 | T1 | 100 | Alarms exist on bounce rate, complaint rate and send volume. | List alarms and their targets. e.g. CloudWatch alarms. | Alarm list |  |
| MAIL-07 | A | - | Emails render correctly in Gmail, Outlook (desktop and web) and Apple Mail, including dark mode. | Send test messages to each client or use a rendering service; screenshot. | Screenshots |  |
| MAIL-08 | T1 | 10 | Users can turn notifications off per category and unsubscribe links work. | Find preference settings; click an unsubscribe link in a test mail. | Code lines, test result |  |
