# Apache Kafka with python

Python library for kafka:

- kafka-python
- confluent-kafka-python
- aiokafka

## Setup

```shell
python3 -m venv venv
source venv/bin/activate
pip install -U pip wheel
pip install kafka-python zstandard
```

## Producer.py

```python
from kafka import KafkaProducer
from time import sleep
import json

# Configure the producer
producer = KafkaProducer(
    bootstrap_servers=['localhost:19092'],
    key_serializer=lambda k: k.encode('utf-8'),
    value_serializer=lambda v: json.dumps(v).encode('utf-8'),
    compression_type='zstd',
    acks='all'
)

# Send message
future = producer.send('topic1', {'message': 'Amir Bashiri'})
result = future.get(timeout=10)
print(f"Sent to partition {result.partition}, offset {result.offset}")

# future = producer.send('topic1', {'message': 'Amir Bashiri'}, 5)
# result = future.get(timeout=10)
# print(f"Sent to partition {result.partition}, offset {result.offset}")

# for i in range(1000):
#     future = producer.send('topic1', {'number': i}, str(i % 10))
#     result = future.get(timeout=10)
#     print(f"Sent to partition {result.partition}, offset {result.offset}")
#     # sleep(1)

# Close the producer
producer.flush()
producer.close()
```

## Consumer.py

```python
from kafka import KafkaConsumer
import json

# Configure the consumer
consumer = KafkaConsumer(
    'topic1',
    bootstrap_servers=['localhost:19092'],
    group_id='group11',
    auto_offset_reset='earliest',
    enable_auto_commit=True,
    key_deserializer=lambda k: k.decode('utf-8'),
    value_deserializer=lambda v: json.loads(v.decode('utf-8'))
)

# Read message
for message in consumer:
    print(f"Topic: {message.topic}", f"Partition: {message.partition}", f"Offset: {message.offset}", f"Key: {message.key}", f"Value: {message.value}")

# Close the consumer
consumer.close()
```

## References

- <https://kafka-python.readthedocs.io/en/master/apidoc/KafkaProducer.html>
- <https://kafka-python.readthedocs.io/en/master/apidoc/KafkaConsumer.html>
