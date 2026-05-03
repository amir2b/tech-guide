# PostgreSQL Transactions

- ACID
- MVCC (Multi-Version Concurrency Control)

## Basic Transaction Commands

```sql
BEGIN;
-- or
START TRANSACTION;

-- Commit changes
COMMIT;

-- Rollback changes
ROLLBACK;
```

### Basic Transaction Example

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT; -- Both updates succeed together

-- Or if something goes wrong
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
-- Oops, something went wrong!
ROLLBACK; -- No changes are applied
```

## Isolation Levels

### READ COMMITTED (Default)

Can only see committed data. Each query sees a snapshot of data committed before the query began

```sql
-- Session 1
BEGIN;
SELECT * FROM products WHERE id = 1; -- Returns price = 100

-- Session 2 (runs concurrently)
BEGIN;
UPDATE products SET price = 200 WHERE id = 1;
COMMIT;

-- Session 1 (continues)
SELECT * FROM products WHERE id = 1; -- Now returns price = 200 (non-repeatable read!)
COMMIT;
```

### REPEATABLE READ

Transaction sees a snapshot of data as of the start of the transaction

```sql
BEGIN;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

SELECT * FROM products WHERE id = 1; -- Returns price = 100

-- Even if another transaction updates to 200 and commits
SELECT * FROM products WHERE id = 1; -- Still returns price = 100

COMMIT;
```

### SERIALIZABLE

Transactions execute as if serialized

```sql
BEGIN;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- Your operations here

COMMIT;
```
