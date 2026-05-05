# Schema-Registry - API Usage

## schemas

```shell
## Verify registered schema types.
curl http://localhost:8081/schemas/types -s | jq
```

## Subjects

```shell
# List all subjects
curl http://localhost:8081/subjects -s | jq

# Register a new version of a schema under the subject "Kafka-key"
curl -X POST http://localhost:8081/subjects/Kafka-key/versions -H "Content-Type: application/vnd.schemaregistry.v1+json" \
    --data '{"schema": "{\"type\": \"string\"}"}' -s | jq

# Register a new version of a schema under the subject "Kafka-value"
curl -X POST http://localhost:8081/subjects/Kafka-value/versions -H "Content-Type: application/vnd.schemaregistry.v1+json" \
    --data '{"schema": "{\"type\":\"record\",\"name\":\"User\",\"fields\":[{\"name\":\"id\",\"type\":\"int\"},{\"name\":\"name\",\"type\":\"string\"}]}"}' -s | jq

# Update schema under the subject "Kafka-value"
curl -X POST http://localhost:8081/subjects/Kafka-value/versions -H "Content-Type: application/vnd.schemaregistry.v1+json" \
    --data '{"schema": "{\"type\":\"record\",\"name\":\"User\",\"fields\":[{\"name\":\"id\",\"type\":\"int\"},{\"name\":\"name\",\"type\":\"string\"},{\"name\":\"email\",\"type\":\"string\",\"default\":\"\"}]}"}' -s | jq

# List all schema versions registered under the subject "Kafka-value"
curl http://localhost:8081/subjects/Kafka-value/versions -s | jq

# Fetch version 1 of the schema registered under subject "Kafka-value"
curl http://localhost:8081/subjects/Kafka-value/versions/1 -s | jq

# Fetch the most recently registered schema under subject "Kafka-value"
curl http://localhost:8081/subjects/Kafka-value/versions/latest -s | jq

# Delete version 3 of the schema registered under subject "Kafka-value"
curl -X DELETE http://localhost:8081/subjects/Kafka-value/versions/3

# Delete all versions of the schema registered under subject "Kafka-value"
curl -X DELETE http://localhost:8081/subjects/Kafka-value

# Check whether a schema has been registered under subject "Kafka-key"
curl -X POST  http://localhost:8081/subjects/Kafka-key -H "Content-Type: application/vnd.schemaregistry.v1+json" \
    --data '{"schema": "{\"type\": \"string\"}"}' -s | jq
```

## schemas

```shell
# Fetch a schema by globally unique id 1
curl http://localhost:8081/schemas/ids/1 -s | jq
```

## Compatibility

```shell
# Test compatibility of a schema with the latest schema under subject "Kafka-value"
curl -X POST http://localhost:8081/compatibility/subjects/Kafka-key/versions/latest -H "Content-Type: application/vnd.schemaregistry.v1+json" \
    --data '{"schema": "{\"type\": \"int\"}"}' -s | jq
```

## config

```shell
# Get top level config
curl http://localhost:8081/config -s | jq

# Update compatibility requirements globally
curl -X PUT http://localhost:8081/config -H "Content-Type: application/vnd.schemaregistry.v1+json" --data '{"compatibility": "NONE"}'

# Update compatibility requirements under the subject "Kafka-value"
curl -X PUT http://localhost:8081/config/Kafka-value -H "Content-Type: application/vnd.schemaregistry.v1+json" --data '{"compatibility": "BACKWARD"}'
```

## References

- <https://docs.confluent.io/platform/current/schema-registry/develop/api.html>
