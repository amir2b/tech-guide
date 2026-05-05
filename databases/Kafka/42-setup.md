# Kafka Connect - Setup

## Docker Compose

```yaml
services:
  kafka-connect:
    image: confluentinc/cp-kafka-connect:8.2.0
    container_name: kafka-connect
    environment:
      CONNECT_REST_ADVERTISED_HOST_NAME: kafka-connect
      CONNECT_REST_PORT: 8083
      CONNECT_BOOTSTRAP_SERVERS: kafka1:9092,kafka2:9092,kafka3:9092
      CONNECT_GROUP_ID: connect-cluster
      CONNECT_CONFIG_STORAGE_TOPIC: _connect-configs
      CONNECT_OFFSET_STORAGE_TOPIC: _connect-offsets
      CONNECT_STATUS_STORAGE_TOPIC: _connect-status
      CONNECT_KEY_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      CONNECT_KEY_CONVERTER_SCHEMAS_ENABLE: "false"
      CONNECT_VALUE_CONVERTER: io.confluent.connect.avro.AvroConverter
      CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL: http://schema-registry:8081
      CONNECT_INTERNAL_KEY_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      CONNECT_INTERNAL_VALUE_CONVERTER: org.apache.kafka.connect.json.JsonConverter
      CONNECT_INTERNAL_KEY_CONVERTER_SCHEMAS_ENABLE: "false"
      CONNECT_INTERNAL_VALUE_CONVERTER_SCHEMAS_ENABLE: "false"
    ports:
      - "8083:8083"
    volumes:
      - ./kafka-connect-plugins:/usr/share/confluent-hub-components
```

### Kafka-UI environments

- KAFKA_CLUSTERS_0_KAFKACONNECT_0_NAME: Kafka Connect
- KAFKA_CLUSTERS_0_KAFKACONNECT_0_ADDRESS: http://kafka-connect:8083

## library

- <https://www.confluent.io/hub/debezium/debezium-connector-postgresql>
- <https://www.confluent.io/hub/confluentinc/kafka-connect-elasticsearch>

## Docker Compose for PostgreSQL

```yaml
services:
  postgres:
    image: postgres:18.2-alpine
    container_name: postgres
    environment:
      TZ: Asia/Tehran
      POSTGRES_PASSWORD: password
    ports:
      - 5432:5432
    volumes:
      - postgres-data:/var/lib/postgresql
    command: |
      postgres
        -c wal_level=logical
        -c max_replication_slots=10
        -c max_wal_senders=10

  adminer:
    image: adminer:standalone
    ports:
      - 8182:8080
    depends_on:
      - postgres

volumes:
  postgres-data:
```

### PostgreSQL CDC Requirements

- wal_level=logical - Enables logical decoding for change capture 
- max_replication_slots=10 - Allows multiple replication slots
- max_wal_senders=10 - Supports multiple concurrent WAL senders

### Create a database and a table

```sql
CREATE DATABASE sample;

-- switch to sample database

CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    product_name VARCHAR(200),
    quantity INTEGER,
    total DECIMAL(10,2),
    status VARCHAR(50),
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

ALTER TABLE public.users REPLICA IDENTITY FULL;
ALTER TABLE public.orders REPLICA IDENTITY FULL;
```

## Docker Compose for Elasticsearch

```yaml
services:
  elasticsearch:
    image: elasticsearch:8.19.14
    container_name: elasticsearch
    environment:
      discovery.type: single-node
      xpack.security.http.ssl.enabled: false
      ELASTIC_PASSWORD: ${ELASTIC_PASSWORD:-password}
    ports:
      - 9200:9200
    volumes:
      - elastic-data:/usr/share/elasticsearch/data
    deploy:
      resources:
        limits:
          memory: 500M

  kibana:
    image: kibana:9.3.0
    container_name: kibana
    environment:
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
      ELASTICSEARCH_USERNAME: kibana_system
      ELASTICSEARCH_PASSWORD: ${KIBANA_PASSWORD:-password}
    ports:
      - 5601:5601
    depends_on:
      - elasticsearch

volumes:
  elastic-data:
```

### Reset kibana_system password

```shell
curl -X POST -u "elastic:${ELASTIC_PASSWORD:-password}" -H "Content-Type: application/json" http://127.0.0.1:9200/_security/user/kibana_system/_password -d "{\"password\":\"${KIBANA_PASSWORD:-password}\"}"
```

## References

- <https://hub.docker.com/r/confluentinc/cp-kafka-connect>
