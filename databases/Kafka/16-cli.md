# Kafka - CLI

```shell

docker exec -it --workdir /opt/kafka/ kafka bash
```

## kafka-broker-api-versions.sh

```shell
## Retrieve broker version information.
bin/kafka-broker-api-versions.sh --bootstrap-server localhost:9092
```

## kafka-topics.sh

```shell
## Print usage information
bin/kafka-topics.sh --help

## List all available topics
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list

## Create a new topic
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic topic1
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic topic2 --partitions 5
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic topic3 --partitions 5 --replication-factor 2

## List details for the given topics.
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic topic1

## Delete a topic.
bin/kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic topic1
```

## kafka-console-producer.sh

You can stop the producer client with `Ctrl-C` at any time.

```shell
## Write some events into the topic
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic topic1
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic topic1 --compression-codec zstd
```

## kafka-console-consumer.sh

You can stop the consumer client with `Ctrl-C` at any time.

```shell
## Read the events
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic1
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic1 --from-beginning
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic1 --from-beginning --group group1
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic1 --offset 0 --partition 0
```

## kafka-consumer-groups.sh

```shell
## Describe consumer group and list offset lag (number of messages not yet processed) related to given group.   
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --all-groups
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group group1
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group group1 --members --verbose
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group group1 --state

## Reset offsets of consumer group. scenarios: [shift-by], [to-offset], [to-earliest], [to-current], [to-latest], [by-duration], [to-datetime], [from-file]
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --reset-offsets --group group1 --topic topic1 --execute --to-earliest
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --reset-offsets --group group1 --topic topic1 --execute --to-latest

## Delete offsets of consumer group. Supports one consumer group at the time, and multiple topics.
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --delete-offsets --group group1 --topic topic1

## Pass in groups to delete topic partition offsets and ownership information over the entire consumer group.
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --delete --group group1
```

## References

- <https://kafka.apache.org/42/operations/basic-kafka-operations/>
- <https://kafka.apache.org/42/configuration/topic-configs/>
