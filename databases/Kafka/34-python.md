# Schema-Registry with python

Python library for kafka:

- confluent-kafka

## Setup

```shell
python3 -m venv venv
source venv/bin/activate
pip install -U pip wheel
pip install "confluent-kafka[avro]" fastavro
```

## Producer.py

```python
import time
from confluent_kafka import Producer
from confluent_kafka.schema_registry import SchemaRegistryClient
from confluent_kafka.schema_registry.avro import AvroSerializer
from confluent_kafka.serialization import SerializationContext, MessageField

TOPIC = 'events'
AVRO_SCHEMA_STR = """
{
  "type": "record",
  "name": "UserEvent",
  "namespace": "com.example",
  "fields": [
    {"name": "user_id",   "type": "string"},
    {"name": "event",     "type": "string"},
    {"name": "timestamp", "type": "long", "logicalType": "timestamp-millis"}
  ]
}
"""

schema_registry_client = SchemaRegistryClient({"url": "http://localhost:8081"})

avro_serializer = AvroSerializer(
    schema_registry_client=schema_registry_client,
    schema_str=AVRO_SCHEMA_STR,
    # Optional: transform dict → object before serialising
    to_dict=lambda obj, ctx: obj,
)
# schema_registry_client.set_compatibility(f"{TOPIC}-value", "BACKWARD")

producer = Producer({"bootstrap.servers": "localhost:19092"})

def delivery_report(err, msg):
    if err:
        print(f"[ERROR] Delivery failed: {err}")
    else:
        print(
            f"[OK] Delivered to {msg.topic()} "
            f"[partition {msg.partition()}] @ offset {msg.offset()}"
        )

EVENTS = ["login", "purchase", "logout", "page_view", "add_to_cart"]

for i, event_name in enumerate(EVENTS):
    record = {
        "user_id": f"user-{i + 1:04d}",
        "event": event_name,
        "timestamp": int(time.time() * 1000),   # ms since epoch
    }

    producer.produce(
        topic=TOPIC,
        key=record["user_id"],
        value=avro_serializer(
            record,
            SerializationContext(TOPIC, MessageField.VALUE),
        ),
        on_delivery=delivery_report,
    )
 
    producer.poll(0)   # trigger callbacks without blocking
 
producer.flush()       # wait for all in-flight messages to finish
print("Done – all messages delivered.")
```

## Consumer.py

```python
from confluent_kafka import Consumer
from confluent_kafka.schema_registry import SchemaRegistryClient
from confluent_kafka.schema_registry.avro import AvroDeserializer
from confluent_kafka.serialization import SerializationContext, MessageField

schema_registry_client = SchemaRegistryClient({"url": "http://localhost:8081"})

avro_deserializer = AvroDeserializer(
    schema_registry_client=schema_registry_client,
    from_dict=lambda obj, ctx: obj,   # keep as plain dict; subclass if needed
)

consumer = Consumer(
    {
        "bootstrap.servers": "localhost:19092",
        "group.id": 'group1',
        "auto.offset.reset": "earliest",
        "enable.auto.commit": True,
    }
)

consumer.subscribe(['events'])

while True:
    msg = consumer.poll(timeout=1.0)   # block up to 1 s per iteration

    if msg is None:
        continue   # no message within timeout – keep polling

    record = avro_deserializer(
        msg.value(),
        SerializationContext(msg.topic(), MessageField.VALUE),
    )
 
    print(
        f"[RECV] key={msg.key().decode()} | "
        f"user_id={record['user_id']} | "
        f"event={record['event']} | "
        f"timestamp={record.get('timestamp', -1)}"
    )
```
