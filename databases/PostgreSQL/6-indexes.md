# indexes

<https://www.postgresql.org/docs/current/indexes.html>

## Sample

```sql
CREATE TABLE test1 (
    id integer,
    content varchar
);

SELECT content FROM test1 WHERE id = constant;

CREATE INDEX test1_id_index ON test1 (id);
```

## B-Tree (Balanced Tree)

```sql
CREATE INDEX idx_btree_users_email ON users USING btree (email);
```

- Equality comparisons (=)
- Range queries (<, <=, >, >=, BETWEEN)
- Sorting operations (ORDER BY)
- Unique constraints and primary keys
- Columns with high cardinality (many unique values)

## GIN (Generalized Inverted Index)

```sql
CREATE INDEX idx_gin_articles_content ON articles USING gin(to_tsvector('english', content));

CREATE INDEX idx_gin_users_skills ON users USING gin(skills);
```

- Full-text search
- Array data types (contains, overlaps)
- JSONB data (key/value queries)
- tsvector columns
- Columns containing multiple values

## BRIN (Block Range INdex)

```sql
CREATE INDEX idx_brin_created_at ON logs USING brin(created_at);
```

- Very large tables (millions+ rows)
- Data that correlates with physical storage order
- Time-series data (e.g., log tables)
- Columns where values naturally cluster

## Hash

```sql
CREATE INDEX idx_hash_users_status ON users USING hash (status);
```

- Simple equality comparisons only (=)
- When you never need range queries or sorting
- Columns with evenly distributed values

## GiST (Generalized Search Tree)

```sql
CREATE INDEX idx_gist_locations ON locations USING gist (coordinates);
```

- Geometric data (points, polygons, circles)
- Full-text search (alternative to GIN)
- Range types (overlaps, contains)
- Spatial data with PostGIS
- Fuzzy string matching

## SP-GiST (Space-Partitioned GiST)

```sql
CREATE INDEX idx_spgist_ips ON networks USING spgist (ip_range);
```

- Quad-trees (2D points)
- K-D trees (multi-dimensional data)
- Radix trees (strings with common prefixes)
- Network addresses (IP ranges)
- Text data with prefix sharing
