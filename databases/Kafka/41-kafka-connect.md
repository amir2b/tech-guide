# Kafka Connect

Kafka Connect is a component of Apache Kafka that provides a scalable, reliable framework for streaming data between Apache Kafka and other data systems (databases, storage, search engines, etc.). It runs as a separate process and uses connectors to move data in or out of Kafka without writing custom producer/consumer code.

Kafka Connect features include:

- A common framework for Kafka connectors - Kafka Connect standardizes integration of other data systems with Kafka, simplifying connector development, deployment, and management
- Distributed and standalone modes - scale up to a large, centrally managed service supporting an entire organization or scale down to development, testing, and small production deployments
- REST interface - submit and manage connectors to your Kafka Connect cluster via an easy to use REST API
- Automatic offset management - with just a little information from connectors, Kafka Connect can manage the offset commit process automatically so connector developers do not need to worry about this error prone part of connector development
- Distributed and scalable by default - Kafka Connect builds on the existing group management protocol. More workers can be added to scale up a Kafka Connect cluster.
- Streaming/batch integration - leveraging Kafka’s existing capabilities, Kafka Connect is an ideal solution for bridging streaming and batch data systems

## Source and Sink Connectors

- Source Connector: ingests data from an external system and publishes it into one or more Kafka topics.
  - Example: A JDBC source connector reads rows from a PostgreSQL table and writes each row as a Kafka message.
- Sink Connector: consumes records from Kafka topics and writes them to an external system.
  - Example: An Elasticsearch sink connector reads messages from a Kafka topic and indexes them into Elasticsearch.

Both connectors manage tasks (parallel workers) that perform the actual data movement.

## Distributed Connect Workers

In distributed mode, multiple Connect worker processes run together as a single logical cluster. Key features:

- Scalability: Add or remove workers dynamically; tasks are automatically rebalanced across workers.
- Fault tolerance: If a worker fails, its tasks are reassigned to other healthy workers.
- Configuration management: Connector configurations are stored in a Kafka topic (connect-configs) and can be submitted/updated via a REST API.
- Offsets storage: Source connector offsets are saved in a Kafka topic (connect-offsets) so that after a restart or rebalance, data ingestion can resume from the right position.

Workers communicate with each other and coordinate using internal Kafka topics. In contrast, standalone mode runs a single worker (useful for development/testing).

## Converters

Converters serialize the data format of Kafka messages (keys and values) independently. Common converters:

- JsonConverter: plain JSON with schema or schemaless.
- AvroConverter: Compact binary with schema support (requires Schema Registry).
- ProtobufConverter
- StringConverter
- ByteArrayConverter.

Each connector specifies key.converter and value.converter. Converters also handle schemas and subject naming for Schema Registry integration.

## Error Handling

Error Handling options:

- Dead Letter Queue (DLQ) – Configure a Kafka topic to receive malformed or failing records. The connector writes failed records (plus error details) to the DLQ instead of stopping.
- Tolerance – Set errors.tolerance to none (fail immediately), all (skip all errors), or none for specific error types.
- Retries – errors.retry.timeout, errors.retry.delay.max.ms – retry transient errors (e.g., network timeouts) before failing or sending to DLQ.
- Logging – Detailed error logs can be enabled per connector.

Example DLQ configuration:

```text
errors.tolerance=all
errors.deadletterqueue.topic.name=my-dlq
errors.deadletterqueue.topic.replication.factor=1
```

## Change data capture (CDC)

![debezium-architecture](files/debezium-architecture.png)

## References

- <https://kafka.apache.org/documentation/#connect>
- <https://kafka.apache.org/42/configuration/kafka-connect-configs/>
- <https://debezium.io/documentation/reference/3.5/connectors/postgresql.html>
- <https://docs.confluent.io/kafka-connectors/elasticsearch/current/overview.html>
