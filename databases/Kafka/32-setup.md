# Schema-Registry - Setup

## Docker compose

```yaml
services:
  schema-registry:
    image: confluentinc/cp-schema-registry:8.2.0
    container_name: schema-registry
    environment:
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
      SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS: kafka1:9092,kafka2:9092,kafka3:9092
    ports:
      - "8081:8081"
```

### Kafka-UI environments:

- KAFKA_CLUSTERS_0_SCHEMAREGISTRY: http://schema-registry:8081

## References

- <https://hub.docker.com/r/confluentinc/cp-schema-registry>
