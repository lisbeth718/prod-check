# File uploads

Gate: uploads

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| UPL-01 | T0 | - | Size and type limits are enforced on the server. | Read upload handler; upload an oversize file and a mismatched type. | Code lines, test result |  |
| UPL-02 | T2 | - | Files are malware-scanned before being served to other users. Applies at T1 when files are served to other users. | Find the scan step and the quarantine state. e.g. ClamAV or a scanning service. | Code lines, config |  |
| UPL-03 | T0 | invoice | Files are stored where they cannot execute: private bucket unless intended, short-lived signed URLs, separate origin or `Content-Disposition: attachment`. | Read storage policy; fetch a stored file URL and read headers; try to run an uploaded HTML or script file. | Bucket policy, headers |  |
| UPL-04 | T1 | 10 | Lifecycle rules delete temp files and old uploads. | Read storage lifecycle rules. e.g. bucket lifecycle config in the object storage console or CLI. | Rule list |  |
| UPL-05 | T0 | invoice | (gate: uploads) Public buckets cannot be listed, and signed upload URLs are scoped to key, content type and size. | Request the bucket root anonymously and expect a refusal; read the signed-URL creation code for key, content-type and max-size conditions and a short expiry. | Request result, code lines |  |
