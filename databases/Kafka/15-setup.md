# Kafka - Setup

## Docker cli command

```shell
docker run -d  \
  --name kafka \
  -e KAFKA_NODE_ID=1 \
  -e KAFKA_PROCESS_ROLES=broker,controller \
  -e KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT \
  -e KAFKA_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093 \
  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 \
  -e KAFKA_CONTROLLER_LISTENER_NAMES=CONTROLLER \
  -e KAFKA_CONTROLLER_QUORUM_VOTERS=1@localhost:9093 \
  -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 \
  -e KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR=1 \
  -e KAFKA_TRANSACTION_STATE_LOG_MIN_ISR=1 \
  -p 9092:9092 \
  apache/kafka:4.2.0
```

## Docker compose

The official Apache Kafka images run with a specific internal user (UID 1000) to enhance security. If you use a bind mount (a folder you manually created on your host), Kafka may not have permission to write to it.

```yaml
services:
  kafka1:
    image: apache/kafka:4.2.0
    container_name: kafka1
    hostname: kafka1
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,INTERNAL:PLAINTEXT
      KAFKA_LISTENERS: EXTERNAL://:19092,INTERNAL://:9092,CONTROLLER://:9093
      KAFKA_ADVERTISED_LISTENERS: EXTERNAL://192.168.50.10:19092,INTERNAL://kafka1:9092
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka1:9093,2@kafka2:9093,3@kafka3:9093
      KAFKA_LOG_DIRS: /var/lib/kafka/data
    ports:
      - "19092:19092"
    volumes:
      - ./data1:/var/lib/kafka/data

  kafka2:
    image: apache/kafka:4.2.0
    container_name: kafka2
    hostname: kafka2
    environment:
      KAFKA_NODE_ID: 2
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,INTERNAL:PLAINTEXT
      KAFKA_LISTENERS: EXTERNAL://:29092,INTERNAL://:9092,CONTROLLER://:9093
      KAFKA_ADVERTISED_LISTENERS: EXTERNAL://192.168.50.10:29092,INTERNAL://kafka2:9092
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka1:9093,2@kafka2:9093,3@kafka3:9093
      KAFKA_LOG_DIRS: /var/lib/kafka/data
    ports:
      - "29092:29092"
    volumes:
      - ./data2:/var/lib/kafka/data

  kafka3:
    image: apache/kafka:4.2.0
    container_name: kafka3
    hostname: kafka3
    environment:
      KAFKA_NODE_ID: 3
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,INTERNAL:PLAINTEXT
      KAFKA_LISTENERS: EXTERNAL://:39092,INTERNAL://:9092,CONTROLLER://:9093
      KAFKA_ADVERTISED_LISTENERS: EXTERNAL://192.168.50.10:39092,INTERNAL://kafka3:9092
      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka1:9093,2@kafka2:9093,3@kafka3:9093
      KAFKA_LOG_DIRS: /var/lib/kafka/data
    ports:
      - "39092:39092"
    volumes:
      - ./data3:/var/lib/kafka/data
```

## References

- <https://kafka.apache.org/42/getting-started/docker/>
- <https://kafka.apache.org/42/getting-started/quickstart/>
- <https://kafka.apache.org/42/configuration/broker-configs/>
