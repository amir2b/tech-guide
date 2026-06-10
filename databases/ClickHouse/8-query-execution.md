# Query Execution & Performance Tuning

## Execution Model

- Vectorized Execution: ClickHouse processes data in columnar blocks, applying functions to thousands of values at once using CPU SIMD instructions.
- Predicate Pushdown: Filters are applied as early as possible to reduce I/O.
- Partition Pruning: Partitions that cannot satisfy the filter are skipped.
- Primary Key Range Pruning: Sparse indexes locate only the relevant granules.
- Parallel Execution: Queries use multiple CPU cores automatically.

## Key Performance Features

- Primary key sparse index: Allows skipping large ranges of granules without scanning.
- PREWHERE (for WHERE conditions): Evaluates conditions on columns before reading other columns, reducing I/O. Particularly useful for wide tables.
- Sampling: SAMPLE BY clause for approximate query results on large tables.
- Aggregate functions with state combinators (-If, -Merge, -State) for incremental aggregation.
- Joins – prefer left tables to be smaller; use JOIN algorithms (partial_merge, full_sorting_merge).

## Tuning Guidelines

- Schema design:
  - Use low‑cardinality columns with LowCardinality type when possible.
  - Avoid nullable columns (they add overhead).
- Query optimization:
  - Filter on partitioned and sorted columns.
  - Avoid SELECT *; list only needed columns.
  - Use LIMIT for exploratory queries.
  - Pre‑aggregate with materialized views.
- System settings:
  - max_threads – limit parallel threads per query.
  - max_memory_usage – per query memory limit.
  - join_algorithm – choose partial_merge for large tables.
  - prefer_localhost_replica – read from local replica for distributed queries.
