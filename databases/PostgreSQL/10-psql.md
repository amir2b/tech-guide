# PSQL

```sql
-- Listing databases:
SELECT datname FROM pg_database;
\list
\list+
\l

-- Switching databases:
\connect db1
\c db1

-- Listing tables:
SELECT * FROM pg_catalog.pg_tables WHERE schemaname != 'pg_catalog' AND schemaname != 'information_schema';
\dt
\dt+

-- table details:
SELECT column_name, data_type, character_maximum_length, is_nullable, column_default FROM information_schema.columns WHERE table_name = 'table1';
\d table1
\d+ table1
```
