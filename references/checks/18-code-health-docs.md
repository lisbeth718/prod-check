# Code health and documentation

Gate: none (always in scope up to the app tier)

| ID | Tier | Stage | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|---|
| CODE-01 | A | - | Code is readable: clear names, small functions, lint and type checks clean. | Run lint and typecheck; sample the largest functions for readability. | Tool output |  |
| CODE-02 | A | - | No 2,000-line files; code is split by responsibility. | List the largest files. e.g. `find . -type f -not -path './node_modules/*' -not -path './.git/*' -not -name '*lock*' -print0 \| xargs -0 wc -l \| sort -n \| tail`; lockfiles and generated files are excluded. | File sizes |  |
| CODE-03 | A | - | Dead code from abandoned agent attempts is removed. | Run an unused-export tool; look for duplicate modules and commented blocks. e.g. `knip`, `ts-prune`. | Tool output |  |
| CODE-04 | A | - | Every change is reviewed (human or a separate reviewer agent that did not write it) before merge. | Check branch protection and the last 20 merged PRs for reviews. | Settings, PR sample |  |
| CODE-05 | A | - | A branching strategy lets two people or agents work without overwriting each other. | Read contribution notes and branch rules. | Doc lines, settings |  |
| CODE-07 | A | - | Architecture decisions are written down with reasons. | Find a decisions log or ADR folder and read its latest entry. | File paths |  |
| CODE-09 | A | - | A README gets a new engineer running locally in under an hour. | Follow the README on a clean checkout and time it. | Time, blockers |  |
| CODE-10 | T1 | 100 | Documentation exists for whoever inherits the app: architecture, deploy, secrets handling, operations and server setup. At T1 an attested setup document is accepted. | Check docs cover architecture, deploy, secrets handling, operations and server setup. | Doc list, gaps |  |
