# UX and accessibility

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| UX-01 | T1 | Every screen has empty, loading and error states. | Walk the screens with empty data, a slow response and a failing API. | Screen list with results |  |
| UX-02 | T1 | Error messages tell the user what to do. | Trigger common errors and read the text; grep for generic messages. | Message list |  |
| UX-03 | T1 | Forms keep what the user typed when one field fails validation. | Submit an invalid form and check values persist. | Test result |  |
| UX-04 | T1 | Destructive or bulk actions can be undone: soft delete with a restore path where users delete things, and undo on bulk edits. | Find user-facing delete paths, a deleted flag and restore path, and undo on bulk edit. | Code lines, test result |  |
| UX-05 | T1 | The back button does what people expect, because the URL reflects state. | Navigate filters, tabs and modals, then press back. | Test result |  |
| UX-06 | T1 | Keyboard navigation and screen reader support work on core flows. | Tab through signup, login and checkout; run an automated a11y scan. e.g. axe or Lighthouse. | Scan output |  |
| UX-07 | T2 | WCAG 2.2 AA (recommended; 2.1 AA is the common contractual floor) is met where contracts or the European Accessibility Act require it. The Act exempts microenterprises providing services. | Run a full automated scan plus manual keyboard and screen reader pass; check for a conformance statement. | Scan output, statement |  |
| UX-08 | T1 | (gate: multiple locales) Translations are complete, and dates, numbers and currencies are formatted per locale. | Switch each locale; grep for untranslated keys; check formatting calls. | Missing-key list |  |
| UX-09 | T1 | Pages load acceptably on poor connections such as hotel wifi, and the app works on the old hardware and browsers your customers use. | Run a throttled profile and the oldest supported browser; read page weight and load times. e.g. Lighthouse with slow 3G, or devtools throttling. | Metrics |  |
