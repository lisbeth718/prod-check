# Input handling and web security

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| WEB-01 | T1 | Server-side validation covers every input, not only the form. | List handlers; confirm a schema validator runs on each body, query and param. e.g. zod at the handler boundary. | Handler list, code lines |  |
| WEB-02 | T1 | Only parameterized queries are used. | Grep for string-built SQL. Tagged templates (e.g. Prisma `$queryRaw`, Drizzle `sql` templates) are parameterized and safe; `$queryRawUnsafe`, `sql.raw` and string concatenation are not. e.g. ``grep -rEn '(SELECT\|INSERT\|UPDATE\|DELETE)[^`]*\$\{' src``; `grep -rEn 'queryRawUnsafe\|sql\.raw' src`. | grep output |  |
| WEB-03 | T1 | No XSS: no unsanitized raw HTML rendering; user HTML or markdown is sanitized. | Grep for raw HTML sinks; check sanitizer use. e.g. `dangerouslySetInnerHTML`, DOMPurify. | grep output, code lines |  |
| WEB-04 | T1 | CSRF: state-changing handlers check origin or use tokens. | List mutating routes; confirm origin or token check on plain API routes (framework server actions may check origin built in). | Route list, code lines |  |
| WEB-05 | T0 | The rest of the OWASP Top 10 is reviewed: SSRF on URL-fetching features, open redirects, insecure deserialization. | Grep for server-side fetch of user URLs, redirect params, unsafe parsers; test with internal addresses. | Findings list |  |
| WEB-06 | T0 | Text fields handle apostrophes (O'Brien), accents, emoji and right-to-left text in storage, display, search and email. | Submit a fixture string in each field type and follow it through DB, UI, search, export and email. | Fixture trace |  |
| WEB-07 | T0 | Security headers are on every response: CSP, HSTS, X-Content-Type-Options, frame-ancestors, Referrer-Policy. | Request several routes and read headers. e.g. `curl -sI https://host/ \| grep -Ei 'content-security\|strict-transport\|x-content\|referrer\|frame-ancestors\|x-frame-options'`. | Header output |  |
| WEB-08 | T1 | (gate: accounts) CORS never reflects an arbitrary Origin together with `Access-Control-Allow-Credentials: true`; authenticated routes use an explicit origin allowlist. | Read CORS config; send an Origin header to authenticated routes and check the response. e.g. `curl -sI -H 'Origin: https://evil.test' https://host/api/me` must not echo that origin with credentials allowed. | Config lines, response headers |  |
| WEB-09 | T1 | Duplicate submits are handled: disabled button plus server-side idempotency. | Read form code and server handler; submit the same request four times and count records. | Code lines, test result |  |
