# Code health and documentation

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| CODE-01 | T1 | Code is readable by a human: clear names, small functions, lint and type checks clean. | Run lint and typecheck; sample the largest functions for readability. | Tool output |  |
| CODE-02 | A | No 2,000-line files; code is split by responsibility. | List the largest files. e.g. `find . -type f -not -path './node_modules/*' -not -path './.git/*' -not -name '*lock*' -print0 \| xargs -0 wc -l \| sort -n \| tail`; lockfiles and generated files are excluded. | File sizes |  |
| CODE-03 | A | Dead code from abandoned agent attempts is removed. | Run an unused-export tool; look for duplicate modules and commented blocks. e.g. `knip`, `ts-prune`. | Tool output |  |
| CODE-04 | T1 | Every change is reviewed (human or a separate reviewer agent that did not write it) before merge. | Check branch protection and the last 20 merged PRs for reviews. | Settings, PR sample |  |
| CODE-05 | A | A branching strategy lets two people or agents work without overwriting each other. | Read contribution notes and branch rules. | Doc lines, settings |  |
| CODE-07 | A | Architecture decisions are written down with reasons. | Find a decisions log or ADR folder and read its latest entry. | File paths |  |
| CODE-09 | T1 | Documentation exists for whoever inherits the app: architecture, deploy, secrets handling, operations and server setup, and a README that gets a new engineer running locally in under an hour. At T1 an attested setup document is accepted. | Check docs cover architecture, deploy, secrets handling, operations and server setup; follow the README on a clean checkout and time it. | Doc list, time, blockers |  |

<!-- Retired IDs: CODE-06 (merged into SEC-04), CODE-08 (merged into CODE-09). Numbers are not reused. -->
