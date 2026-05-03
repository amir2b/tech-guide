# What is Partitioning?

Partitioning is splitting a large table into smaller physical pieces (partitions) while still treating it as a single logical table. Each partition holds a subset of the data based on specific criteria.

- Improved Query Performance - Partition pruning skips irrelevant partitions
- Easier Maintenance - Work with smaller pieces (VACUUM, REINDEX, backup)
- Better Data Management - Drop old partitions instead of DELETE
- Parallel Processing - Can scan partitions in parallel

## Range Partitioning

Divide data based on value ranges (most common for time-series data)

```sql
CREATE TABLE orders (
    id SERIAL,
    user_id INTEGER,
    amount DECIMAL,
    created_at TIMESTAMPTZ DEFAULT NOW()
) PARTITION BY RANGE (created_at);

-- Create partitions
CREATE TABLE orders_2026_01 PARTITION OF orders FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');
CREATE TABLE orders_2026_02 PARTITION OF orders FOR VALUES FROM ('2026-02-01') TO ('2026-03-01');
CREATE TABLE orders_2026_03 PARTITION OF orders FOR VALUES FROM ('2026-03-01') TO ('2026-04-01');

-- Create indexes on partitions
CREATE INDEX idx_orders_2026_01_user ON orders_2026_01(user_id);
CREATE INDEX idx_orders_2026_02_user ON orders_2026_02(user_id);
CREATE INDEX idx_orders_2026_03_user ON orders_2026_03(user_id);

CREATE VIEW recent_events AS
SELECT * FROM events
WHERE event_time >= NOW() - INTERVAL '3 months';

EXPLAIN SELECT * FROM orders
WHERE created_at BETWEEN '2026-02-18' AND '2026-02-28';

DROP TABLE orders_2026_01;
ALTER TABLE orders DETACH PARTITION orders_2026_01;
-- ALTER TABLE orders ATTACH PARTITION orders_2026_01 FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');

VACUUM orders_2026_01;
```

## List Partitioning

Divide based on discrete values

```sql
CREATE TABLE customers (
    id SERIAL,
    name TEXT,
    country TEXT
) PARTITION BY LIST (country);

-- Create partitions
CREATE TABLE customers_usa PARTITION OF customers FOR VALUES IN ('USA');
CREATE TABLE customers_canada PARTITION OF customers FOR VALUES IN ('Canada');
CREATE TABLE customers_other PARTITION OF customers DEFAULT;
```

## Hash Partitioning

Distribute data using a hash function (good for load balancing)

```sql
CREATE TABLE transactions (
    id SERIAL,
    user_id INTEGER,
    amount DECIMAL
) PARTITION BY HASH (user_id);

-- Create partitions
CREATE TABLE transactions_p0 PARTITION OF transactions FOR VALUES WITH (MODULUS 4, REMAINDER 0);
CREATE TABLE transactions_p1 PARTITION OF transactions FOR VALUES WITH (MODULUS 4, REMAINDER 1);
CREATE TABLE transactions_p2 PARTITION OF transactions FOR VALUES WITH (MODULUS 4, REMAINDER 2);
CREATE TABLE transactions_p3 PARTITION OF transactions FOR VALUES WITH (MODULUS 4, REMAINDER 3);
```
