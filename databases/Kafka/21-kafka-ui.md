# Kafka UI

Kafbat UI is a versatile, fast, lightweight, and flexible web interface designed to monitor and manage Apache Kafka® clusters. Created by developers for developers, it offers an intuitive way to gain visibility into your data flows, quickly identify and resolve issues, and maintain peak performance.

<https://ui.docs.kafbat.io>

## Features

- Topic Insights – View essential topic details including partition count, replication status, and custom configurations.
- Configuration Wizard – Set up and configure your Kafka clusters directly through the UI.
- Multi-Cluster Management – Monitor and manage all your Kafka clusters in one unified interface.
- Metrics Dashboard – Track key Kafka metrics in real time with a streamlined, lightweight dashboard.
- Kafka Brokers Overview – Inspect brokers, including partition assignments and controller status.
- Consumer Group Details – Analyze parked offsets per partition, and monitor both combined and partition-specific lag.
- Message Browser – Explore messages in JSON, plain text, or Avro encoding formats. Live view is supported, enriched with user-defined CEL message filters.
- Dynamic Topic Management – Create and configure new topics with flexible, real-time settings.
- Pluggable Authentication – Secure your UI using OAuth 2.0 (GitHub, GitLab, Google), LDAP, or basic authentication.
- Cloud IAM Support – Integrate with GCP IAM, Azure IAM, and AWS IAM for cloud-native identity and access management.
- Managed Kafka Service Support – Full support for Azure EventHub, Google Cloud Managed Service for Apache Kafka, and AWS Managed Streaming for Apache Kafka (MSK)—both server-based and serverless.
- Custom SerDe Plugin Support – Use built-in serializers/deserializers like AWS Glue and Smile, or create your own custom plugins.
- Role-Based Access Control – Manage granular UI permissions with RBAC.
- Data Masking – Obfuscate sensitive data in topic messages to enhance privacy and compliance.
- MCP Server - Model Context Protocol Server

## Docker compose

```yaml
services:
  kafka-ui:
    image: kafbat/kafka-ui:v1.5.0
    container_name: kafka-ui
    environment:
      DYNAMIC_CONFIG_ENABLED: 'true'
      KAFKA_CLUSTERS_0_NAME: docker
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka1:9092,kafka2:9092,kafka3:9092
    ports:
      - "8080:8080"
    volumes:
      - kafka-ui-data:/etc/kafkaui

volumes:
  kafka-ui-data:
```

## References

- <https://ui.docs.kafbat.io/configuration/configuration-file>
