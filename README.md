# MongoDB-Dump

Script
```bash
#!/bin/bash
set -uo pipefail

HOST=127.0.0.1
PORT=27017
USER=<USER-ID>
PASS='<PASSWORD>'
AUTHDB=admin
BACKUP_PATH="/var/dbbackups/backup_$(date +%Y%m%d)"
PARALLEL_JOBS=6

mkdir -p "$BACKUP_PATH"

DBS=$(mongosh --host="$HOST" --port="$PORT" --username="$USER" --password="$PASS" \
  --authenticationDatabase="$AUTHDB" --quiet \
  --eval="db.getMongo().getDBNames().join('\n')")

echo "$DBS" | grep -vE '^(admin|local|config)$' | xargs -P "$PARALLEL_JOBS" -I {} \
  mongodump --host="$HOST" --port="$PORT" --username="$USER" --password="$PASS" \
    --authenticationDatabase="$AUTHDB" --numParallelCollections=1 --gzip \
    --db="{}" --out="$BACKUP_PATH" 2>&1

echo "Backup script completed."

```

Backup Location:
```bash
mkdir -p /var/dbbackups
sudo chown rentalbuxdblive:rentalbuxdblive /var/dbbackups
```

Command to run backup
```bash
#Best to run in a tmux session
tmux
nohup ./backup.sh > dump_parallel.log 2>&1 &
```
