---
description: Apache Kafka quick reference - distributed streaming, topics, producers, consumers, partitions, replication, and event-driven architecture.
---

# Apache Kafka Quick Reference

## What is Kafka?

**Apache Kafka** = Distributed event streaming platform

**Simply put:**
- Messaging system on steroids
- Publish-Subscribe model with high performance
- Event log storage
- Real-time data pipeline

**In Platform/DevOps environments:**
- Metrics streaming (Prometheus → Kafka → Analytics)
- Log aggregation (centralized log collection)
- Event-driven architecture (microservice communication)
- CI/CD events (build/deploy events)
- Audit logging (change tracking)

---

## Core Concepts (With Simple Examples)

### 1. Topic
A "channel" or "category" where messages are sent.

**Analogy:** YouTube channel
- Topic = "build-events" (like a YouTube channel)
- Messages = videos on that channel

```bash
# Example topics
build-events # Build events (success, failure)
deployment-events # Deployment events
user-activity # User activity log
metrics # Metrics data
```

### 2. Producer
The sender of messages (writer).

**Analogy:** Person uploading videos to YouTube

```python
# Producer example (Python)
from kafka import KafkaProducer
import json

producer = KafkaProducer(
bootstrap_servers='localhost:9092',
value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

# Send message
message = {
'event': 'build_completed',
'pipeline': 'myapp-build',
'status': 'success',
'duration': 120
}

producer.send('build-events', message)
producer.flush()
```

### 3. Consumer
The reader of messages (reader).

**Analogy:** Person watching YouTube videos

```python
# Consumer example (Python)
from kafka import KafkaConsumer
import json

consumer = KafkaConsumer(
'build-events',
bootstrap_servers='localhost:9092',
value_deserializer=lambda m: json.loads(m.decode('utf-8')),
group_id='notification-service'
)

# Read messages
for message in consumer:
event = message.value
print(f"Build: {event['pipeline']} - {event['status']}")

if event['status'] == 'failed':
send_slack_notification(event)
```

### 4. Broker
Kafka server (message storage and delivery).

**Analogy:** YouTube servers

- One Kafka cluster = multiple brokers (servers)
- Brokers store topics
- Brokers distribute load

```
Kafka Cluster:
Broker 1 (server-1:9092)
Broker 2 (server-2:9092)
Broker 3 (server-3:9092)
```

### 5. Partition
Topic split into parts (parallelism).

**Analogy:** YouTube playlist split into sections

```
Topic: build-events
Partition 0: [msg1, msg3, msg5]
Partition 1: [msg2, msg4, msg6]
Partition 2: [msg7, msg8, msg9]
```

**Why important:**
- Multiple consumers can read in parallel (1 partition = 1 consumer)
- Scalability
- Load balancing

### 6. Consumer Group
Group of consumers (teamwork).

**Analogy:** Work teams that divide the work

```
Consumer Group: notification-service
Consumer 1 → Partition 0
Consumer 2 → Partition 1
Consumer 3 → Partition 2

Consumer Group: analytics-service
Consumer 1 → Partition 0, 1, 2 (all messages)
```

**Important:** Each consumer group receives all messages!

### 7. Offset
Message position in a partition.

**Analogy:** Bookmark position in a book

```
Partition 0:
Offset 0: {"event": "build_started"}
Offset 1: {"event": "build_completed"}
Offset 2: {"event": "build_failed"}
↑
Current offset (consumer last read)
```

**Commit offset:** "I've read up to here" marker.

---

## Zookeeper's Role

**Apache Zookeeper** = Coordination service (Kafka's "control tower")

**What it does:**
- Broker registry (which brokers are alive)
- Leader election (which broker is the leader)
- Configuration management
- Partition metadata storage

**Important:** Kafka 3.x+ **no longer requires Zookeeper** (KRaft mode)!

```
Old architecture (Kafka 2.x):

Kafka Cluster
Broker 1 
Broker 2 → Zookeeper Ensemble
Broker 3 ZK1
ZK2
ZK3

New architecture (Kafka 3.x+ KRaft):

Kafka Cluster (self-managed)
Broker 1 (controller)
Broker 2
Broker 3
```

**Modern Kafka:** Zookeeper deprecated, KRaft will be the default.

---

## Simple Example: Build Notification System

**Scenario:** Slack notification when Tekton build completes.

### 1. Producer (Tekton Pipeline)

```yaml
# Tekton Task: Send Kafka message
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
name: kafka-notify
spec:
params:
- name: event-type
- name: status
- name: pipeline-name

steps:
- name: send-message
image: confluentinc/cp-kafka:latest
script: |
#!/bin/bash
echo '{"event": "$(params.event-type)", "status": "$(params.status)", "pipeline": "$(params.pipeline-name)"}' | \
kafka-console-producer \
--bootstrap-server kafka-broker:9092 \
--topic build-events
```

**Usage in Pipeline:**
```yaml
- name: notify-build-complete
taskRef:
name: kafka-notify
params:
- name: event-type
value: build_completed
- name: status
value: success
- name: pipeline-name
value: myapp-build
```

### 2. Consumer (Notification Service)

```python
# notification_service.py
from kafka import KafkaConsumer
import json
import requests

# Kafka consumer setup
consumer = KafkaConsumer(
'build-events',
bootstrap_servers='kafka-broker:9092',
value_deserializer=lambda m: json.loads(m.decode('utf-8')),
group_id='notification-service',
auto_offset_reset='earliest' # Start from beginning
)

SLACK_WEBHOOK = "https://hooks.slack.com/services/XXX"

# Process events
for message in consumer:
event = message.value

print(f"Received: {event}")

# Send Slack notification
if event['status'] == 'success':
emoji = ''
elif event['status'] == 'failed':
emoji = ''
else:
emoji = ''

slack_message = {
"text": f"{emoji} Build {event['pipeline']}: {event['status']}"
}

requests.post(SLACK_WEBHOOK, json=slack_message)
print(f"Notification sent for {event['pipeline']}")
```

### 3. Consumer (Analytics Service)

```python
# analytics_service.py
from kafka import KafkaConsumer
import json
from prometheus_client import Counter, start_http_server

# Prometheus metrics
build_total = Counter('builds_total', 'Total builds', ['status'])

# Kafka consumer
consumer = KafkaConsumer(
'build-events',
bootstrap_servers='kafka-broker:9092',
value_deserializer=lambda m: json.loads(m.decode('utf-8')),
group_id='analytics-service', # Different group!
auto_offset_reset='earliest'
)

# Start Prometheus exporter
start_http_server(8000)

# Process events
for message in consumer:
event = message.value

# Update Prometheus metrics
build_total.labels(status=event['status']).inc()

print(f"Metrics updated: {event['status']}")
```

**Result:**
- **1 Producer** (Tekton) → Kafka topic
- **2 Consumers** (Notification, Analytics) → Same topic, different groups
- Every consumer receives all messages!

---

## Kafka CLI Cheat Sheet

### Topic Management

```bash
# List topics
kafka-topics --bootstrap-server localhost:9092 --list

# Create topic
kafka-topics --bootstrap-server localhost:9092 \
--create \
--topic build-events \
--partitions 3 \
--replication-factor 2

# Describe topic
kafka-topics --bootstrap-server localhost:9092 \
--describe \
--topic build-events

# Delete topic
kafka-topics --bootstrap-server localhost:9092 \
--delete \
--topic build-events

# Increase partitions
kafka-topics --bootstrap-server localhost:9092 \
--alter \
--topic build-events \
--partitions 5
```

### Producer (Console)

```bash
# Send messages interactively
kafka-console-producer \
--bootstrap-server localhost:9092 \
--topic build-events

# Send from file
cat messages.txt | kafka-console-producer \
--bootstrap-server localhost:9092 \
--topic build-events

# With key (for partition selection)
kafka-console-producer \
--bootstrap-server localhost:9092 \
--topic build-events \
--property "parse.key=true" \
--property "key.separator=:"
# Then type: key1:message1
```

### Consumer (Console)

```bash
# Consume from latest
kafka-console-consumer \
--bootstrap-server localhost:9092 \
--topic build-events

# Consume from beginning
kafka-console-consumer \
--bootstrap-server localhost:9092 \
--topic build-events \
--from-beginning

# With consumer group
kafka-console-consumer \
--bootstrap-server localhost:9092 \
--topic build-events \
--group my-group

# Show keys
kafka-console-consumer \
--bootstrap-server localhost:9092 \
--topic build-events \
--property print.key=true \
--property key.separator=":"

# With timestamp
kafka-console-consumer \
--bootstrap-server localhost:9092 \
--topic build-events \
--property print.timestamp=true
```

### Consumer Group Management

```bash
# List consumer groups
kafka-consumer-groups --bootstrap-server localhost:9092 --list

# Describe consumer group (offsets, lag)
kafka-consumer-groups --bootstrap-server localhost:9092 \
--describe \
--group notification-service

# Reset offsets (replay messages)
kafka-consumer-groups --bootstrap-server localhost:9092 \
--group notification-service \
--topic build-events \
--reset-offsets \
--to-earliest \
--execute

# Reset to specific offset
kafka-consumer-groups --bootstrap-server localhost:9092 \
--group notification-service \
--topic build-events \
--reset-offsets \
--to-offset 100 \
--execute

# Delete consumer group
kafka-consumer-groups --bootstrap-server localhost:9092 \
--delete \
--group old-group
```

### Performance & Monitoring

```bash
# Producer performance test
kafka-producer-perf-test \
--topic test-topic \
--num-records 1000000 \
--record-size 1000 \
--throughput -1 \
--producer-props bootstrap.servers=localhost:9092

# Consumer performance test
kafka-consumer-perf-test \
--bootstrap-server localhost:9092 \
--topic test-topic \
--messages 1000000

# Get topic size (messages)
kafka-run-class kafka.tools.GetOffsetShell \
--broker-list localhost:9092 \
--topic build-events
```

---

## Python Producer/Consumer Examples

### Producer (Detailed)

```python
from kafka import KafkaProducer
from kafka.errors import KafkaError
import json
import time

# Producer configuration
producer = KafkaProducer(
bootstrap_servers=['localhost:9092'],

# Serialization
key_serializer=lambda k: k.encode('utf-8'),
value_serializer=lambda v: json.dumps(v).encode('utf-8'),

# Performance tuning
batch_size=16384, # Batch size (bytes)
linger_ms=10, # Wait time before sending batch
compression_type='gzip', # Compression (gzip, snappy, lz4)

# Reliability
acks='all', # Wait for all replicas (strongest guarantee)
retries=3, # Retry on failure
)

# Send message (async)
def send_event(event_type, data):
future = producer.send(
'build-events',
key=event_type, # Key for partitioning
value=data
)

try:
# Block until sent (or timeout)
record_metadata = future.get(timeout=10)
print(f"Sent to partition {record_metadata.partition}, offset {record_metadata.offset}")
except KafkaError as e:
print(f"Failed to send: {e}")

# Usage
send_event('build_started', {
'pipeline': 'myapp-build',
'commit': 'abc123',
'timestamp': time.time()
})

# Flush & close
producer.flush()
producer.close()
```

### Consumer (Detailed)

```python
from kafka import KafkaConsumer
from kafka.errors import KafkaError
import json
import signal
import sys

# Graceful shutdown handler
def signal_handler(sig, frame):
print('Shutting down...')
consumer.close()
sys.exit(0)

signal.signal(signal.SIGINT, signal_handler)

# Consumer configuration
consumer = KafkaConsumer(
'build-events',
bootstrap_servers=['localhost:9092'],

# Deserialization
key_deserializer=lambda k: k.decode('utf-8'),
value_deserializer=lambda v: json.loads(v.decode('utf-8')),

# Consumer group
group_id='notification-service',

# Offset management
auto_offset_reset='earliest', # 'earliest' or 'latest'
enable_auto_commit=True, # Auto-commit offsets
auto_commit_interval_ms=5000, # Commit every 5 seconds

# Performance
max_poll_records=500, # Max records per poll
fetch_min_bytes=1, # Min data before returning
fetch_max_wait_ms=500, # Max wait time
)

# Process messages
for message in consumer:
print(f"Topic: {message.topic}")
print(f"Partition: {message.partition}")
print(f"Offset: {message.offset}")
print(f"Key: {message.key}")
print(f"Value: {message.value}")
print("---")

# Process event
event = message.value
if event.get('status') == 'failed':
send_alert(event)

# Manual commit example
consumer = KafkaConsumer(
'build-events',
bootstrap_servers=['localhost:9092'],
group_id='my-group',
enable_auto_commit=False # Manual commit
)

for message in consumer:
process_message(message.value)

# Commit offset after successful processing
consumer.commit()
```

### Advanced: Consumer with Retry

```python
from kafka import KafkaConsumer
import json
import time

consumer = KafkaConsumer(
'build-events',
bootstrap_servers=['localhost:9092'],
group_id='notification-service',
enable_auto_commit=False
)

def process_with_retry(message, max_retries=3):
for attempt in range(max_retries):
try:
# Process message
send_notification(message.value)
return True
except Exception as e:
print(f"Attempt {attempt + 1} failed: {e}")
if attempt < max_retries - 1:
time.sleep(2 ** attempt) # Exponential backoff
return False

for message in consumer:
success = process_with_retry(message)

if success:
# Commit only on success
consumer.commit()
else:
# Send to dead letter queue
send_to_dlq(message)
consumer.commit() # Still commit to avoid reprocessing
```

---

## Docker Compose Setup (Quick Start)

```yaml
# docker-compose.yml
version: '3'

services:
# Kafka broker (KRaft mode - no Zookeeper!)
kafka:
image: confluentinc/cp-kafka:7.5.0
hostname: kafka
container_name: kafka
ports:
- "9092:9092"
environment:
KAFKA_NODE_ID: 1
KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: 'CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT'
KAFKA_ADVERTISED_LISTENERS: 'PLAINTEXT://localhost:9092'
KAFKA_PROCESS_ROLES: 'broker,controller'
KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka:29093'
KAFKA_LISTENERS: 'PLAINTEXT://0.0.0.0:9092,CONTROLLER://kafka:29093'
KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'
KAFKA_LOG_DIRS: '/tmp/kraft-combined-logs'
CLUSTER_ID: 'MkU3OEVBNTcwNTJENDM2Qk'

# Kafka UI (optional - for visualization)
kafka-ui:
image: provectuslabs/kafka-ui:latest
container_name: kafka-ui
ports:
- "8080:8080"
environment:
KAFKA_CLUSTERS_0_NAME: local
KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
depends_on:
- kafka
```

**Usage:**
```bash
# Start Kafka
docker-compose up -d

# Check logs
docker-compose logs -f kafka

# Access Kafka UI
open http://localhost:8080

# Create topic
docker exec kafka kafka-topics --bootstrap-server localhost:9092 \
--create --topic test-topic --partitions 3 --replication-factor 1

# Send message
docker exec -it kafka kafka-console-producer \
--bootstrap-server localhost:9092 --topic test-topic

# Consume messages
docker exec -it kafka kafka-console-consumer \
--bootstrap-server localhost:9092 --topic test-topic --from-beginning

# Stop
docker-compose down
```

---

## Real-world Use Cases

### 1. Log Aggregation

```
Application Logs → Kafka → ELK Stack

[App1] 
[App2] → Kafka (topic: logs) → Logstash → Elasticsearch → Kibana
[App3] 
```

**Benefits:**
- Centralized log collection
- Decoupling (app doesn't depend on Elasticsearch)
- Replay (reprocess if needed)

### 2. Metrics Pipeline

```
Prometheus → Kafka → Long-term Storage

[Prometheus] → Kafka (topic: metrics) → [Thanos/Cortex]
→ [InfluxDB]
→ [Analytics DB]
```

### 3. Event-driven Microservices

```
Order Service → Kafka → Notification + Inventory

[Order Created] → Kafka (topic: orders)
→ Notification Service (send email)
→ Inventory Service (update stock)
→ Analytics Service (track sales)
```

### 4. Change Data Capture (CDC)

```
Database Changes → Kafka → Downstream Services

[PostgreSQL] → Debezium → Kafka (topic: db.changes)
→ Search Index (Elasticsearch)
→ Cache (Redis)
→ Data Warehouse
```

### 5. CI/CD Event Bus

```python
# CI/CD Pipeline events in Kafka

# Producer (Tekton Pipeline)
kafka_producer.send('cicd-events', {
'event': 'pipeline_started',
'pipeline': 'myapp-build',
'commit': 'abc123',
'branch': 'main'
})

# Consumer 1: Notification service
# Consumer 2: Metrics collector
# Consumer 3: Audit logger
# Consumer 4: Dashboard updater
```

---

## Kafka vs RabbitMQ vs Redis

| Feature | Kafka | RabbitMQ | Redis Streams |
|---------|-------|----------|---------------|
| **Type** | Event streaming | Message broker | In-memory data store |
| **Throughput** | Very high | Medium | High |
| **Persistence** | Disk (durable) | Disk (optional) | Memory (optional disk) |
| **Message retention** | Days/weeks | Until consumed | Manual trimming |
| **Use case** | Logs, events, streaming | Task queues, RPC | Caching, real-time |
| **Ordering** | Per partition | Per queue | Per stream |
| **Replay** | Yes | No | Yes |
| **Complexity** | High | Medium | Low |

**When to use Kafka:**
- High throughput
- Event replay needed
- Log aggregation
- Stream processing

**When to use RabbitMQ:**
- Complex routing
- Task queues
- RPC patterns

**When to use Redis:**
- Simple pub/sub
- In-memory speed
- Temporary data

---

## Platform-specific Example

**Scenario:** Build pipeline events analytics

```python
# producer.py (Tekton task sends build events)
from kafka import KafkaProducer
import json
import os

producer = KafkaProducer(
bootstrap_servers=os.getenv('KAFKA_BOOTSTRAP_SERVERS', 'kafka:9092'),
value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

# Send build event
build_event = {
'event_type': 'build_completed',
'pipeline_name': os.getenv('PIPELINE_NAME'),
'pipeline_run': os.getenv('PIPELINE_RUN_NAME'),
'status': os.getenv('BUILD_STATUS'), # success/failed
'duration_seconds': int(os.getenv('BUILD_DURATION')),
'commit_sha': os.getenv('GIT_COMMIT'),
'component': os.getenv('COMPONENT_NAME'),
'timestamp': time.time()
}

producer.send('platform-build-events', build_event)
producer.flush()
```

```python
# consumer.py (Analytics service)
from kafka import KafkaConsumer
from prometheus_client import Counter, Histogram, start_http_server
import json

# Prometheus metrics
builds_total = Counter('platform_builds_total', 'Total builds', ['component', 'status'])
build_duration = Histogram('platform_build_duration_seconds', 'Build duration', ['component'])

# Kafka consumer
consumer = KafkaConsumer(
'platform-build-events',
bootstrap_servers='kafka:9092',
value_deserializer=lambda m: json.loads(m.decode('utf-8')),
group_id='analytics-service'
)

# Start Prometheus exporter
start_http_server(8000)

# Process events
for message in consumer:
event = message.value

# Update metrics
builds_total.labels(
component=event['component'],
status=event['status']
).inc()

build_duration.labels(
component=event['component']
).observe(event['duration_seconds'])
```

**Deployment (Kubernetes):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: build-analytics
spec:
replicas: 3
template:
spec:
containers:
- name: analytics
image: quay.io/platform/build-analytics:latest
env:
- name: KAFKA_BOOTSTRAP_SERVERS
value: kafka-cluster-kafka-bootstrap:9092
```

---

## Useful Links

- **Kafka Docs**: https://kafka.apache.org/documentation/
- **Kafka Python Client**: https://kafka-python.readthedocs.io/
- **Confluent Kafka**: https://docs.confluent.io/
- **Kafka Tutorials**: https://kafka-tutorials.confluent.io/
- **Strimzi (Kafka on K8s)**: https://strimzi.io/

---

**Event streaming - build real-time data pipelines!** 
