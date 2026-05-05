# KSqlDB

ksqlDB is a streaming SQL engine built on top of Apache Kafka. It lets you write real-time data processing logic using simple, familiar SQL syntax instead of writing complex Java/Scala code with Kafka Streams.

Think of it as: "PostgreSQL for streaming data" – but instead of querying static tables, you query live, moving streams and get continuously updating results.

## How It Works

ksqlDB turns Kafka topics into virtual tables (called streams and tables), then runs SQL queries that execute forever, processing new data as it arrives.

```sql
-- Example: Filter fraudulent credit card transactions
CREATE STREAM fraud_alerts AS
  SELECT card_id, amount, location
  FROM transactions
  WHERE amount > 10000;
```

As new transactions flow into Kafka, this query runs continuously, pushing any fraud matches to a new Kafka topic.

## Key Concepts

- Stream: An immutable, append-only sequence of events (like a Kafka topic)
- Table: A mutable view of the latest state per key (like a changelog)
- Push query: Runs forever, emits results as new data arrives
- Pull query: Like a normal SQL query – returns current state and stops

## Example

```sql
-- Stream example
CREATE STREAM user_clicks (
    user_id VARCHAR,
    page VARCHAR,
    click_time TIMESTAMP
) WITH (KAFKA_TOPIC = 'clicks-topic', VALUE_FORMAT = 'JSON');

-- Table example
CREATE TABLE product_inventory (
    product_id VARCHAR PRIMARY KEY,
    quantity INT
) WITH (KAFKA_TOPIC = 'inventory-changelog', VALUE_FORMAT = 'JSON');

-- Push Query
SELECT user_id, page, click_time
FROM user_clicks
EMIT CHANGES;

-- Pull Query
SELECT quantity
FROM product_inventory
WHERE product_id = 'P1001';
```

## Common Use Cases

- Real-time dashboards (live metrics, monitoring)
- Fraud detection (alert within milliseconds)
- IoT data processing (filter, aggregate sensor readings)
- Data transformation (ETL within Kafka – no separate pipeline)
- Event streaming apps (joining streams, windowed aggregations)

## Example in Action

Scenario: A website anonymizing user clicks by removing IP addresses.

```sql
-- 1. Declare the source stream
CREATE STREAM raw_clicks (
  user_id VARCHAR,
  ip VARCHAR,
  click_url VARCHAR,
  ts BIGINT
) WITH (kafka_topic='clicks', value_format='JSON');

-- 2. Create anonymized output stream
CREATE STREAM clean_clicks AS
  SELECT 
    user_id, 
    'REDACTED' AS ip,
    click_url,
    AS_TIMESTAMP(ts) AS event_time
  FROM raw_clicks;
```

Every click runs through this query instantly.

## References

- <https://docs.confluent.io/platform/current/ksqldb/operate-and-deploy/installation/server-config.html>
