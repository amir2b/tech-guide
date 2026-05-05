## Kafka Rest Proxy

## API

```shell
# Get a list of topics
curl "http://localhost:8082/topics" -s | jq

# Get info about one topic
curl "http://localhost:8082/topics/_connect-offsets" -s | jq

## Get your Cluster ID
curl "http://localhost:8082/v3/clusters" -s | jq '.data[0].cluster_id'

## Create a new topic
curl -X POST "http://localhost:8082/v3/clusters/<cluster-id>/topics" -H "Content-Type: application/json" --data '{
    "topic_name": "topic1",
    "partitions_count": 3,
    "replication_factor": 2,
    "configs": [
        {
            "name": "cleanup.policy",
            "value": "compact"
        },
        {
            "name": "compression.type",
            "value": "gzip"
        }
    ]
}' -s | jq

## Produce
curl -X POST "http://localhost:8082/topics/topic1" -H "Content-Type: application/vnd.kafka.json.v2+json" \
    --data '{"records": [{"key":"k1", "value": {"foo":"bar"}}]}' -s | jq

# 1. Create a new consumer instance (Consumer Group: "my_json_consumer")
curl -X POST http://localhost:8082/consumers/my_json_consumer -H "Content-Type: application/vnd.kafka.v2+json" \
  --data '{"name": "my_consumer_instance", "format": "json", "auto.offset.reset": "earliest"}'

# 2. Subscribe the consumer to a topic (e.g., "jsontest")
curl -X POST http://localhost:8082/consumers/my_json_consumer/instances/my_consumer_instance/subscription -H "Content-Type: application/vnd.kafka.v2+json" \
  --data '{"topics":["topic1"]}'
  
# 3. Consume messages from the topic
curl -X GET http://localhost:8082/consumers/my_json_consumer/instances/my_consumer_instance/records -H "Accept: application/vnd.kafka.json.v2+json"

# 4. Clean up: Delete the consumer instance
curl -X DELETE -H "Accept: application/vnd.kafka.v2+json" \
  http://localhost:8082/consumers/my_json_consumer/instances/my_consumer_instance
```

## References

- <https://docs.confluent.io/platform/current/kafka-rest/quickstart.html>
- <https://docs.confluent.io/platform/current/kafka-rest/production-deployment/rest-proxy/config.html>
