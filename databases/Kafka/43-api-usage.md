# Kafka Connect - API Usage

The following are the currently supported REST API endpoints:

## connector-plugins

```shell
## return a list of connector plugins installed in the Kafka Connect cluster.
curl http://localhost:8083/connector-plugins -s | jq
```

## connectors

```shell
## return a list of active connectors
curl http://localhost:8083/connectors

## Create a new source connector
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{
  "name": "postgres-cdc-connector",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "database.hostname": "192.168.50.10",
    "database.port": "5432",
    "database.user": "postgres",
    "database.password": "password",
    "database.dbname": "sample",
    "plugin.name": "pgoutput",
    "table.include.list": "public.users,public.orders",
    "topic.prefix": "postgres",
    "topic.delimiter": "_",
    "snapshot.mode": "initial"
  }
}' -s | jq

# Check connector status
curl -s http://localhost:8083/connectors/postgres-cdc-connector/status | jq

## Create a new sink connector
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{
  "name": "elasticsearch-sink-connector",
  "config": {
    "connector.class": "io.confluent.connect.elasticsearch.ElasticsearchSinkConnector",
    "tasks.max": "1",
    "topics": "postgres_public_users,postgres_public_orders",
    "connection.url": "http://192.168.50.10:9200",
    "connection.username": "elastic",
    "connection.password": "password",
    "key.ignore": "false",
    "schema.ignore": "true",
    "behavior.on.null.values": "delete",
    "write.method": "upsert",
    "transforms": "unwrap,extractKey",
    "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
    "transforms.unwrap.drop.tombstones": "false",
    "transforms.unwrap.delete.handling.mode": "rewrite",
    "transforms.extractKey.type": "org.apache.kafka.connect.transforms.ExtractField$Key",
    "transforms.extractKey.field": "id"
  }
}' -s | jq

# Check connector status
curl -s http://localhost:8083/connectors/elasticsearch-sink-connector/status | jq
```

## References

- <https://kafka.apache.org/42/kafka-connect/user-guide/>
