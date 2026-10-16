# Email

Gate: transactional email

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| MAIL-01 | T0 | The email account is out of the provider sandbox in the sending region. | Read account status. e.g. Amazon SES `aws sesv2 get-account` shows ProductionAccessEnabled. | Command output |  |
| MAIL-02 | T0 | Per sending domain: DKIM verified, a custom return-path (MAIL FROM) domain so SPF aligns, DMARC present and moving from none to quarantine or reject. | Query DNS and provider verification status. e.g. `dig TXT _dmarc.example.com`; `aws sesv2 get-email-identity`. | DNS output, identity status |  |
| MAIL-03 | T0 | Every send uses a tracking configuration; bounce and complaint events reach a handler that stops mailing those addresses; account-level suppression is on. | Find the send call and event routing; check the handler suppresses addresses. e.g. SES configuration set, SNS or EventBridge destination, account suppression list. | Code lines, config |  |
| MAIL-04 | T0 | Bounce rate is well under 5% and complaint rate well under 0.1%. | Read provider reputation metrics for the last 30 days. e.g. SES reputation dashboard. | Metric values |  |
| MAIL-05 | T0 | Email sending credentials are scoped to sending only, restricted to verified identities, and used by nothing else. | Read the credential's policy and where it is used. | Policy document |  |
| MAIL-06 | T0 | Alarms exist on bounce rate, complaint rate and send volume. | List alarms and their targets. e.g. CloudWatch alarms. | Alarm list |  |
| MAIL-07 | T1 | Emails render correctly in Gmail, Outlook (desktop and web) and Apple Mail, including dark mode. | Send test messages to each client or use a rendering service; screenshot. | Screenshots |  |
| MAIL-08 | T1 | Users can turn notifications off per category and unsubscribe links work. | Find preference settings; click an unsubscribe link in a test mail. | Code lines, test result |  |
