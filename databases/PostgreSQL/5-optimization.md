# PostgreSQL Query Planning & Optimization

## Basic EXPLAIN

Shows the query plan without executing it.

```sql
EXPLAIN SELECT * FROM users WHERE email = 'test@example.com';
```

## EXPLAIN ANALYZE

Executes te query and shows actual execution times and row counts.

```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';
```

## EXPLAIN (ANALYZE, BUFFERS)

Shows cache hit rates and I/O operations. Buffers: Shared hits/reads/dirtied/written

```sql
EXPLAIN (ANALYZE, BUFFERS) SELECT * FROM users WHERE email = 'test@example.com';
```

## EXPLAIN (VERBOSE, ANALYZE)

Verbose Output

```sql
EXPLAIN (VERBOSE, ANALYZE) SELECT * FROM customers;
```

### Key EXPLAIN Output Components

#### Node Types

- Seq Scan: Sequential table scan
- Index Scan: Index lookup
- Index Only Scan: All data in index
- Bitmap Heap Scan: Bitmap index scan
- Nested Loop: Nested loop join
- Hash Join: Hash-based join
- Merge Join: Merge-based join

#### Cost Values (cost=0.28..8.29 rows=1 width=100)

- cost=0.28..8.29: First number = startup cost, second = total cost
- rows=1: Estimated rows returned
- width=100: Estimated average row width in bytes

#### Timing Information (with ANALYZE)

- actual time=0.12..15.34: Actual execution time in ms
