# PostgreSQL Backup & Restore

A PostgreSQL backup is a copy of the data that you can use to recover the database later.

- Logical backups
- Physical backups
- PITR (Point-in-Time Recovery)
- WAL (Write-Ahead Log)

## pg_dump

Exports a PostgreSQL database as an SQL script or to other formats.

```shell
pg_dump [OPTION]... [DBNAME]
pg_dump -U postgres -d db1 -f /backup/db1.sql
pg_dump -U postgres -d db1 -F tar -f /backup/db1.tar
```

## pg_dumpall

The pg_dumpall tool is a command-line utility that you can use to create logical backups of the entire PostgreSQL cluster, including all databases, schemas, roles, and other cluster-wide objects.

```sql
pg_dumpall -U postgres > /backup/all_databases.sql
pg_dumpall -U postgres --schema-only > /backup/schemas.sql
```

## pg_restore

```shell
psql -U postgres -c "drop database db1"
psql -U postgres -c "create database db1"

pg_restore -U postgres -d db1 /backup/db1.tar
```

## pg_basebackup

<https://www.postgresql.org/docs/current/app-pgbasebackup.html>

```shell
## Enable WAL Summarization
psql -U postgres <<EOF
    ALTER SYSTEM SET summarize_wal = 'on';
    SELECT pg_reload_conf();
EOF

## Check WAL Summarization is enable
psql -U postgres -c "SHOW summarize_wal;"
ps xf | grep walsummarizer

## Full backup
pg_basebackup -U postgres --pgdata=/backup/full/

## Incremental backup
pg_basebackup -U postgres --pgdata=/backup/inc1 --incremental=/backup/full/backup_manifest
pg_basebackup -U postgres --pgdata=/backup/inc2 --incremental=/backup/inc1/backup_manifest

## Restoring backups
pg_combinebackup /backup/full/ /backup/inc1/ /backup/inc2/ -o /backup/final/
chown -R postgres:postgres /backup/final/
rm -f /backup/final/postmaster.pid

mv /var/lib/postgresql/18/docker /var/lib/postgresql/18/docker.bak
mv /backup/final /var/lib/postgresql/18/docker
```
