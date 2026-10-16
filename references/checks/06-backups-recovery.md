# Backups and recovery

Gate: none (always in scope up to the app tier)

| ID | Tier | Check | How to verify | Evidence to record | Attest |
|---|---|---|---|---|---|
| BKP-01 | T0 | Nightly off-box backups exist, in a separate bucket and with credentials separate from the app. | List backup jobs, target and credentials; confirm the app cannot delete them; check the newest backup age. | Job config, newest backup timestamp |  |
| BKP-02 | T1 | Continuous archiving enables point-in-time recovery. | Check WAL archiving is on and recent; confirm retention window. e.g. `SHOW archive_mode;`, WAL-G or pgBackRest `info`. | Config values, archive age |  |
| BKP-03 | T0 | A restore has actually been performed; the date of the last drill is recorded. None ever is a FAIL. | Look for drill logs or a restore script run; ask for the date. | Drill date, log excerpt | attest |
| BKP-04 | T0 | Every app sharing the same database instance has its own backup and restore drill, not just this app. | List databases on the instance; map each to a backup job and a drill record. e.g. `\l` in psql. | Database-to-backup table |  |
| BKP-05 | T2 | A disaster recovery plan is written down and tested at least yearly. | Ask for the plan and last test record. | Plan location, last test date | attest |
