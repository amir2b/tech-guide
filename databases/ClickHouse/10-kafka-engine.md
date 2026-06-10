# ClickHouse Kafka Engine

ClickHouse can consume data from Apache Kafka in real time using the Kafka engine combined with materialized views to persist data into MergeTree tables.

Data Flow:

```text
Kafka Topic
    ↓
Kafka Engine Table
    ↓
Materialized View
    ↓
MergeTree Table
```

## Prepration

### Kafka

```yaml
services:
  kafka:
    image: apache/kafka:4.2.0
    container_name: kafka
    hostname: kafka
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,INTERNAL:PLAINTEXT
      KAFKA_LISTENERS: EXTERNAL://:19092,INTERNAL://:9092,CONTROLLER://:9093
      KAFKA_ADVERTISED_LISTENERS: EXTERNAL://192.168.56.10:19092,INTERNAL://kafka:9092
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_LOG_DIRS: /var/lib/kafka/data
    ports:
      - "19092:19092"
    volumes:
      - kafka-data:/var/lib/kafka/data

  kafka-ui:
    image: kafbat/kafka-ui:v1.5.0
    container_name: kafka-ui
    environment:
      DYNAMIC_CONFIG_ENABLED: 'true'
      KAFKA_CLUSTERS_0_NAME: docker
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
    ports:
      - "8080:8080"
    volumes:
      - kafka-ui-data:/etc/kafkaui
    depends_on:
      - kafka

volumes:
  kafka-data:
  kafka-ui-data:
```

### Python Generator

```shell
python3 -m venv venv
source venv/bin/activate
pip install -U pip wheel
pip install kafka-python
```

```python
from kafka import KafkaProducer
from datetime import datetime
import json
import random
import time

producer = KafkaProducer(bootstrap_servers='localhost:19092', value_serializer=lambda v: json.dumps(v).encode('utf-8'))

try:
    while True:
        record = {
            'id': random.randint(1, 10000),
            'timestamp': datetime.now().isoformat(timespec='seconds'),
            'value': round(random.uniform(0, 100), 2),
            'category': random.choice(['A', 'B', 'C'])
        }
        producer.send('test_topic', value=record)
        print(f"Sent: {record}")
        time.sleep(1)
except KeyboardInterrupt:
    print("\nStopped.")
finally:
    producer.close()
```

## Kafka Engine Table

- A Kafka engine table acts as a consumer/producer interface to a Kafka topic.
- It does not store data persistently; it only allows streaming data in and out.

```sql
CREATE TABLE IF NOT EXISTS kafka_queue (
    id UInt32,
    timestamp DateTime,
    value Float32,
    category String
) ENGINE = Kafka
SETTINGS kafka_broker_list = '192.168.56.10:19092',
         kafka_topic_list = 'test_topic',
         kafka_group_name = 'clickhouse_group_1',
         kafka_format = 'JSONEachRow';
```

- You can SELECT from this table to read messages (once) – but usually you want continuous ingestion.

## Materialized View for Ingestion into MergeTree

To continuously move data from Kafka to a persistent MergeTree table, create a materialized view that reads from the Kafka engine and writes to a target table.

Step 1: Create target MergeTree table.

```sql
CREATE TABLE IF NOT EXISTS sensor_data (
    id UInt32,
    timestamp DateTime,
    value Float32,
    category String
) ENGINE = MergeTree()
ORDER BY (timestamp, id);
```

Step 2: Create materialized view that automatically consumes from Kafka and writes to events_mt.

```sql
CREATE MATERIALIZED VIEW sensor_data_consumer TO sensor_data AS
SELECT
    id,
    timestamp,
    value,
    category
FROM kafka_queue;

SELECT * FROM sensor_data ORDER BY timestamp DESC LIMIT 10;
```

- The view is triggered for each block of data read from the Kafka table.
- It transforms the data (e.g., adding event_date) and inserts into the target MergeTree table.

## Important notes

- The materialized view attaches to the source (Kafka engine) table – any INSERT or consumption from Kafka triggers the view logic.
- Background consumption: ClickHouse will poll Kafka automatically if you keep the Kafka table “alive”. To start continuous ingestion, you need to run a SELECT that never ends (e.g., SELECT * FROM kafka_queue), or use the materialized_views setting to let background tasks consume.
- Resilience: Kafka offsets are committed only after data is written to the MergeTree table. If the process crashes, ClickHouse restarts from the last committed offset.
- Multiple topics / formats: You can specify multiple topics, and kafka_format supports many formats (JSONEachRow, Avro, Protobuf, etc.).

## Advanced Patterns

- Multiple materialized views on the same Kafka table – can route different types of messages to different tables.
- Kafka engine as producer: You can INSERT into a Kafka table to write data back to Kafka.
- Transformations in the materialized view: use ClickHouse’s SQL functions to clean, filter, or enrich messages before storing.
- Parallel consumption: Increase kafka_num_consumers to have multiple consumers per table (but note each consumer enforces ordering per partition).

## Example complete ingestion pipeline

```sql
-- 1. Kafka source
CREATE TABLE kafka_raw (
    raw String
) ENGINE = Kafka('broker:9092', 'topic', 'group', 'JSONAsString');

-- 2. Target table
CREATE TABLE clicks (
    ts DateTime,
    user_id UInt64,
    page String
) ENGINE = MergeTree ORDER BY ts;

-- 3. Materialized view parsing JSON
CREATE MATERIALIZED VIEW parse_mv TO clicks AS
SELECT
    JSONExtractString(raw, 'timestamp')::DateTime AS ts,
    JSONExtractInt(raw, 'user_id') AS user_id,
    JSONExtractString(raw, 'page') AS page
FROM kafka_raw
WHERE JSONHas(raw, 'user_id');
```

Once the view is created, any data arriving in Kafka will be automatically loaded into the clicks table in real time, with minimal latency (usually sub‑second).

## References

- <https://clickhouse.com/docs/engines/table-engines/integrations/kafka>
