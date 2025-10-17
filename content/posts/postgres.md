---
title: "Postgres Command Cheatsheet"
date: '2025-10-17T17:11:08+02:00'
draft: false
tags: ["postgres","cheatsheet","database"]
---

A compact Postgres command & workflow cheat‑sheet for daily database tasks. Copy commands and adapt them to your environment.

<!--more-->

## Quick start

Run locally with Docker (Windows PowerShell):

```powershell
docker run --name pg -e POSTGRES_PASSWORD=secret -p 5432:5432 -d postgres:15
# connect with psql from host (psql must be installed)
psql "host=localhost port=5432 user=postgres dbname=postgres password=secret"
```

Install reference:

- Official downloads: <https://www.postgresql.org/download/>

## psql basics

- Connect:

  ```
  psql -h host -p 5432 -U user -d dbname
  ```

- Common interactive commands:
  - \l            — list databases
  - \c dbname     — connect to database
  - \dt           — list tables
  - \d+ table     — describe table
  - \du           — list roles
  - \conninfo     — show current connection
  - \q            — quit

## Create / manage DBs & users

```sql
-- create role and DB
CREATE ROLE deployer WITH LOGIN PASSWORD 's3cret';
CREATE DATABASE myapp OWNER deployer;

-- grant permissions
GRANT CONNECT ON DATABASE myapp TO deployer;
GRANT USAGE ON SCHEMA public TO deployer;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO deployer;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO deployer;
```

## Backup & restore

- Logical backup (single DB):

  ```
  pg_dump -h host -U user -Fc -f mydb.dump mydb
  pg_restore -h host -U user -d mydb_restored mydb.dump
  ```

- Plain SQL:

  ```
  pg_dump -h host -U user -f mydb.sql mydb
  psql -h host -U user -d mydb_restored -f mydb.sql
  ```

- All clusters:

  ```
  pg_dumpall -h host -U postgres -f all.sql
  ```

## Common maintenance

- Vacuum & analyze:

  ```
  VACUUM (VERBOSE, ANALYZE);
  VACUUM FULL;           -- more invasive, exclusive locks
  ANALYZE;               -- update planner statistics
  ```

- Reindex:

  ```
  REINDEX DATABASE mydb;
  ```

- Show long-running queries:

  ```sql
  SELECT pid, now()-pg_stat_activity.query_start AS duration, query, state
  FROM pg_stat_activity
  WHERE state <> 'idle'
  ORDER BY duration DESC;
  ```

## Performance & introspection

- Explain & run:

  ```sql
  EXPLAIN ANALYZE SELECT * FROM users WHERE id = 1;
  ```

- DB size:

  ```sql
  SELECT pg_size_pretty(pg_database_size('mydb'));
  SELECT pg_size_pretty(pg_total_relation_size('schema.table'));
  ```

- Index usage and bloat hints:

  ```sql
  -- list index usage stats
  SELECT relname, seq_scan, idx_scan FROM pg_stat_user_tables;
  ```

## Connection & auth tips

- Example libpq connection string:

  ```
  postgres://user:pass@host:5432/dbname?sslmode=disable
  ```

- Edit authentication in pg_hba.conf and reload:

  ```
  sudo systemctl reload postgresql   # Linux systemd
  net stop postgresql-x64-XX         # Windows (service name varies)
  net start postgresql-x64-XX
  ```

## Docker / Compose snippet

```yaml
# docker-compose.yml
version: "3.9"
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

## Useful troubleshooting commands

- View logs:

  ```
  docker logs -f pg
  journalctl -u postgresql --since "1 hour ago"
  tail -n 200 /var/log/postgresql/postgresql-15-main.log
  ```

- Check active connections:

  ```sql
  SELECT datname, count(*) FROM pg_stat_activity GROUP BY datname;
  ```

## Extensions & extras

- Install/use Postgres extensions:

  ```sql
  CREATE EXTENSION IF NOT EXISTS pg_trgm;
  CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
  ```

- Track slow queries:

  ```
  -- enable pg_stat_statements in postgresql.conf (shared_preload_libraries) then:
  SELECT * FROM pg_stat_statements ORDER BY total_time DESC LIMIT 10;
  ```

## Quick tips

- Prefer role-per-database principle; avoid superuser for apps.
- Keep regular backups and test restores.
- Use EXPLAIN ANALYZE before adding indexes.
- Run VACUUM/ANALYZE regularly or enable autovacuum.
- Secure credentials: use environment variables or secret stores.

That's it — save this post as your Postgres daily reference and extend with project‑specific commands.
