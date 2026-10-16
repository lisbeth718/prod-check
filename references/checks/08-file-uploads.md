# File uploads

Gate: uploads

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| UPL-01 | T1 | Size and type limits are enforced on the server. | Read upload handler; upload an oversize file and a mismatched type. | Code lines, test result |  |
| UPL-02 | T1 | Files are malware-scanned before being served to other users. | Find the scan step and the quarantine state. e.g. ClamAV or a scanning service. | Code lines, config |  |
| UPL-03 | T1 | Files are stored where they cannot execute: private bucket unless intended, short-lived signed URLs, separate origin or `Content-Disposition: attachment`. | Read storage policy; fetch a stored file URL and read headers; try to run an uploaded HTML or script file. | Bucket policy, headers |  |
| UPL-04 | T0 | Lifecycle rules delete temp files and old uploads. | Read storage lifecycle rules. e.g. bucket lifecycle config in the object storage console or CLI. | Rule list |  |
