# Compression & Storage Optimization

## Compression Codecs

- Default: LZ4 – fast, with a good compression ratio. You can enable ZSTD (higher compression, slower) at the table or column level.
- Specialized codecs for specific data patterns:
  - Delta – stores differences between consecutive values (good for time series or monotonically changing data).
  - DoubleDelta – second‑order delta, ideal for sequences with constant change (e.g., timers or incrementing counters).
  - Gorilla – optimized for floating‑point values with small changes (e.g., metrics).
  - T64 – efficient for integers that fit into fewer than 64 bits (e.g., small‑range values like enums or ages).
  - LZ4HC – a high‑compression variant of LZ4 (slower but better ratio).
  - ZSTD(level) – ZSTD with an optional compression level (e.g., ZSTD(3)).

```sql
CREATE TABLE metrics
(
    timestamp DateTime CODEC(Delta, ZSTD),
    value     Float64  CODEC(Gorilla),
    name      String   CODEC(ZSTD(3))
)
ENGINE = MergeTree()
ORDER BY timestamp;

CREATE TABLE user_actions
(
    user_id  UInt64 CODEC(T64),
    age      UInt8  CODEC(T64),
    event    String CODEC(LZ4HC)
)
ENGINE = MergeTree()
ORDER BY user_id;

CREATE TABLE sensor_data
(
    time   DateTime CODEC(DoubleDelta, ZSTD),
    temp   Float32  CODEC(Gorilla, ZSTD),
    counter UInt64  CODEC(Delta, LZ4HC)
)
ENGINE = MergeTree()
ORDER BY time;
```

## LowCardinality

It uses dictionary encoding to efficiently store columns with a small number of distinct values relative to the total row count.

Benefits:

- Storage reduction – dramatically smaller for repeated strings.
- Faster queries – GROUP BY, COUNT(DISTINCT), equality filters (WHERE status = 'active') work on compact keys.
- Better compression – the dictionary itself can be compressed further with codecs like ZSTD.

```sql
CREATE TABLE example (
    status LowCardinality(String),
    city   LowCardinality(String) CODEC(ZSTD)
);
```

## Storage Optimization Tips

- Choose suitable partition key – avoid too many small parts (<1MB) to reduce overhead.
- Set index_granularity (default 8192) – number of rows per granule. Lower values improve point queries but increase index size.
- Use ALTER TABLE ... MODIFY COLUMN ... CODEC to change compression on existing data (background rewrite).
- MergeTree merges – background process combines parts; you can tune merge_with_ttl_timeout, number of background threads, etc.
- Data skipping indices – additional secondary indexes (bloom filters, minmax, set) to skip granules without reading them.
- Sorting groups similar values together, creating longer runs and improving compression ratios.
- Use the smallest practical types:
  - UInt32 instead of UInt64
  - Date instead of DateTime
  - Decimal for exact financial values
