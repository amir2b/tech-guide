# Table Engines (MergeTree Family)

The MergeTree family is the most powerful and flexible engine family in ClickHouse, designed for high‑volume inserts and efficient queries. All MergeTree engines share a common base and add specific features.

## MergeTree

supports partitioning, sorting keys, primary key, data replication (Base engine).

The rule: The PRIMARY KEY must be a prefix of the ORDER BY key.

```sql
CREATE TABLE users
(
    id UInt32,
    name String
)
ENGINE = MergeTree
PRIMARY KEY id;

INSERT INTO users VALUES (1, 'Amir'), (2, 'Bashiri'), (1, 'Amir2');

----------

CREATE TABLE trades
(
    symbol String,
    ts DateTime64(3),
    price Float64,
    volume Float64
)
ENGINE = MergeTree
PARTITION BY toYYYYMM(ts)
ORDER BY (symbol, ts);

INSERT INTO trades VALUES ('BTCUSDT', now() - 1, 1, 1), ('BTCUSDT', now(), 2, 2), ('ETHUSDT', now(), 1, 1);
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/mergetree>

## ReplacingMergeTree

- Deduplicates rows with the same sorting key (ORDER BY table section, not PRIMARY KEY).
- Data deduplication occurs only during a merge. Merging occurs in the background at an unknown time, so you can't plan for it.
- You can run an unscheduled merge using the OPTIMIZE query
- Useful for:
  - CDC
  - Upserts
  - Deduplication

```sql
CREATE TABLE users
(
    id UInt32,
    name String
)
ENGINE = ReplacingMergeTree
ORDER BY id;

INSERT INTO users VALUES (1, 'Amir'), (2, 'Bashiri'), (1, 'Amir2');

SELECT * FROM users;

SELECT * FROM users FINAL;

OPTIMIZE TABLE users;
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/replacingmergetree>

## CoalescingMergeTree

Replaces all rows with the same primary key with a single row that contains the latest non-NULL values for each column. This enables column-level upserts, meaning you can update only specific columns rather than entire rows.

```sql
CREATE TABLE test_table
(
    key UInt64,
    value_int Nullable(UInt32),
    value_string Nullable(String),
    value_date Nullable(Date)
)
ENGINE = CoalescingMergeTree()
ORDER BY key;

INSERT INTO test_table VALUES(1, NULL, NULL, '2025-01-01'), (2, 10, 'test', NULL);
INSERT INTO test_table VALUES(1, 42, 'win', '2025-02-01');
INSERT INTO test_table(key, value_date) VALUES(2, '2025-02-01');

SELECT * FROM test_table;

SELECT * FROM test_table FINAL;

OPTIMIZE TABLE test_table;
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/coalescingmergetree>

## SummingMergeTree

Pre‑aggregates numeric columns for rows with the same sorting key during merges (Automatically sums numeric columns during merges).

```sql
CREATE TABLE summtt
(
    key UInt32,
    value UInt32
)
ENGINE = SummingMergeTree()
ORDER BY key;

INSERT INTO summtt VALUES(1,1),(1,2),(2,1);

SELECT * FROM summtt FINAL;

SELECT key, sum(value) FROM summtt GROUP BY key;
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/summingmergetree>

## AggregatingMergeTree

AggregatingMergeTree stores aggregation states (not final values). It's designed for materialized views that pre-aggregate data, drastically reducing query time for large datasets.

Key points:

- Use *State functions (e.g., sumState, uniqState) for inserts.
- Use *Merge functions (e.g., sumMerge, uniqMerge) in SELECT.
- AggregatingMergeTree merges parts by applying the aggregate functions (e.g., summing intermediate states).
- Ideal for high‑volume real‑time analytics (counters, unique users, histograms, etc.).

```sql
-- Source table (raw data)
CREATE TABLE page_views_raw
(
    event_time DateTime,
    url String,
    user_id UInt64
)
ENGINE = MergeTree()
ORDER BY (event_time);

-- Aggregating table (target)
CREATE TABLE page_views_daily
(
    day Date,
    url String,
    view_count AggregateFunction(sum, UInt8),
    unique_users AggregateFunction(uniq, UInt64)
)
ENGINE = AggregatingMergeTree()
ORDER BY (day, url);

-- Materialized view (automatic aggregation)
CREATE MATERIALIZED VIEW page_views_mv TO page_views_daily AS
SELECT
    toDate(event_time) AS day,
    url,
    sumState(1) AS view_count,
    uniqState(user_id) AS unique_users
FROM page_views_raw
GROUP BY day, url;

-- Insert sample data
INSERT INTO page_views_raw VALUES
    (now(), '/home', 101),
    (now(), '/home', 102),
    (now(), '/about', 101),
    (now(), '/home', 101);

-- Query the aggregated data
SELECT
    day,
    url,
    sumMerge(view_count) AS views,
    uniqMerge(unique_users) AS users
FROM page_views_daily
GROUP BY day, url;
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/aggregatingmergetree>

## CollapsingMergeTree

The CollapsingMergeTree table engine asynchronously deletes (collapses) pairs of rows if all the fields in a sorting key (ORDER BY) are equivalent except for the special field Sign, which can have values of either 1 or -1. Rows without a pair of opposite valued Sign are kept.

- If Sign = 1 it means that the row is a "state" row: a row containing fields which represent a current valid state.
- If Sign = -1 it means that the row is a "cancel" row: a row used for the cancellation of state of an object with the same attributes.

```sql
-- For example, we want to calculate how many pages users checked on some website and how long they visited them for. At some given moment in time, we write the following row with the state of user activity

CREATE TABLE UAct
(
    UserID UInt64,
    PageViews UInt8,
    Duration UInt8,
    Sign Int8
)
ENGINE = CollapsingMergeTree(Sign)
ORDER BY UserID;

INSERT INTO UAct VALUES (1, 5, 146, 1);
INSERT INTO UAct VALUES (1, 5, 146, -1),(1, 6, 185, 1);

SELECT * FROM UAct;

SELECT
    UserID,
    sum(PageViews * Sign) AS PageViews,
    sum(Duration * Sign) AS Duration
FROM UAct
GROUP BY UserID
HAVING sum(Sign) > 0;

SELECT * FROM UAct FINAL;

OPTIMIZE TABLE UAct;
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/collapsingmergetree>

## VersionedCollapsingMergeTree

Same as collapsing but with version column to ensure correct order.

- Allows quick writing of object states that are continually changing.
- Deletes old object states in the background. This significantly reduces the volume of storage.

```sql
-- For example, we want to calculate how many pages users visited on some site and how long they were there. At some point in time we write the following row with the state of user activity

CREATE TABLE UAct
(
    UserID UInt64,
    PageViews UInt8,
    Duration UInt8,
    Sign Int8,
    Version UInt8
)
ENGINE = VersionedCollapsingMergeTree(Sign, Version)
ORDER BY UserID;

INSERT INTO UAct VALUES (1, 5, 146, 1, 1);
INSERT INTO UAct VALUES (1, 5, 146, -1, 1),(1, 6, 185, 1, 2);

SELECT * FROM UAct;

SELECT
    UserID,
    sum(PageViews * Sign) AS PageViews,
    sum(Duration * Sign) AS Duration,
    Version
FROM UAct
GROUP BY UserID, Version
HAVING sum(Sign) > 0;

SELECT * FROM UAct FINAL;
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/versionedcollapsingmergetree>

## GraphiteMergeTree

This engine is designed for thinning and aggregating/averaging (rollup) Graphite (time‑series) data.

### /etc/clickhouse-server/config.d/graphite_rollup.xml

```xml
<clickhouse>
    <graphite_rollup>
        <version_column_name>Version</version_column_name>
        <pattern>
            <regexp>click_cost</regexp>
            <function>any</function>
            <retention>
                <age>0</age>
                <precision>5</precision>
            </retention>
            <retention>
                <age>86400</age>
                <precision>60</precision>
            </retention>
        </pattern>
        <default>
            <function>max</function>
            <retention>
                <age>0</age>
                <precision>60</precision>
            </retention>
            <retention>
                <age>3600</age>
                <precision>300</precision>
            </retention>
            <retention>
                <age>86400</age>
                <precision>3600</precision>
            </retention>
        </default>
    </graphite_rollup>
</clickhouse>
```

```sql
CREATE TABLE graphite_data
(
    Path String,
    Time DateTime,
    Value Float64,
    Version UInt32
)
ENGINE = GraphiteMergeTree('graphite_rollup')
ORDER BY (Path, Time);

INSERT INTO graphite_data (Path, Time, Value, Version) VALUES
    ('click_cost.server1', now() - INTERVAL 1 MINUTE, 0.05, 1),
    ('click_cost.server1', now() - INTERVAL 55 SECOND, 0.07, 1),
    ('click_cost.server1', now() - INTERVAL 50 SECOND, 0.06, 1),
    ('click_cost.server1', now() - INTERVAL 45 SECOND, 0.08, 1),
    ('click_cost.server1', now() - INTERVAL 40 SECOND, 0.04, 1),
    ('click_cost.server2', now() - INTERVAL 1 MINUTE, 0.12, 1),
    ('click_cost.server2', now() - INTERVAL 55 SECOND, 0.11, 1),
    ('click_cost.server2', now() - INTERVAL 50 SECOND, 0.13, 1),
    ('server.cpu.usage', now(), 45.2, 1),
    ('server.memory.used', now(), 8123456789, 1);
```

<https://clickhouse.com/docs/engines/table-engines/mergetree-family/graphitemergetree>

### Common mechanisms

- All MergeTree engines partition data (optional), sort rows by ORDER BY key, and maintain a sparse primary index.
- Background merges combine small data parts into larger ones, applying engine‑specific logic (e.g., collapsing, summing).
- Data is never mutated in place; updates/deletes are implemented via mutations (rewriting parts) or as “deltas” using certain engines.

## References

- <https://clickhouse.com/docs/engines/table-engines/mergetree-family>
- <https://clickhouse.com/docs/sql-reference/statements/optimize>
