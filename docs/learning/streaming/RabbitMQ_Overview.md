# RabbitMQ Overview & Configuration Guide

**Comprehensive guide to RabbitMQ - message broker for distributed systems, microservices, and event-driven architectures.**

---

## Table of Contents

1. [Introduction](#introduction)
2. [Architecture & Concepts](#architecture--concepts)
3. [Exchange Types](#exchange-types)
4. [Queue Configuration](#queue-configuration)
5. [Message Routing Patterns](#message-routing-patterns)
6. [Publisher-Consumer Patterns](#publisher-consumer-patterns)
7. [High Availability & Clustering](#high-availability--clustering)
8. [Federation & Shovel](#federation--shovel)
9. [Security & Authentication](#security--authentication)
10. [Monitoring & Management](#monitoring--management)
11. [Client Libraries](#client-libraries)
12. [Kubernetes Deployment](#kubernetes-deployment)
13. [Performance Tuning](#performance-tuning)
14. [Best Practices](#best-practices)
15. [Troubleshooting](#troubleshooting)

---

## Introduction

### What is RabbitMQ?

**RabbitMQ** is an open-source message broker implementing the **Advanced Message Queuing Protocol (AMQP)**. It enables applications to communicate asynchronously through messages.

**Key Features:**
- **Reliable delivery** - Persistence, acknowledgements, publisher confirms
- **Flexible routing** - Multiple exchange types (direct, topic, fanout, headers)
- **Clustering** - High availability and horizontal scaling
- **Multi-protocol** - AMQP 0-9-1, AMQP 1.0, MQTT, STOMP
- **Management UI** - Web-based monitoring and administration
- **Plugin ecosystem** - Federation, Shovel, delayed messaging, consistent hash exchange

### When to Use RabbitMQ?

**Use Cases:**
- **Microservices communication** - Decouple services with async messaging
- **Event-driven architectures** - Publish events, subscribe to topics
- **Task queues** - Distribute work across workers (e.g., background jobs)
- **Real-time messaging** - Chat applications, notifications
- **Data streaming** - Ingest and process high-volume data streams
- **Request/Reply** - RPC-style communication with correlation IDs

### RabbitMQ vs Kafka

| Feature | RabbitMQ | Apache Kafka |
|---------|----------|--------------|
| **Protocol** | AMQP, MQTT, STOMP | Custom TCP protocol |
| **Messaging Model** | Push-based (broker pushes to consumers) | Pull-based (consumers poll) |
| **Message Retention** | Messages deleted after consumption | Messages retained for configurable period |
| **Use Case** | Task queues, RPC, complex routing | Event streaming, log aggregation |
| **Ordering** | Per-queue ordering | Per-partition ordering |
| **Throughput** | ~20K msgs/sec (single broker) | ~100K+ msgs/sec (cluster) |
| **Latency** | Low latency (<10ms) | Low latency (~5ms) |
| **Message Size** | Suitable for small-medium messages | Optimized for larger messages |

**Recommendation:**
- **RabbitMQ** - Task distribution, RPC, complex routing, low-latency messaging
- **Kafka** - Event sourcing, log aggregation, stream processing, high throughput

---

## Architecture & Concepts

### Core Components

```

Publisher > RabbitMQ Broker > Consumer 


Exchange > Queue 


Routing Keys, Bindings 

```

**Components:**

1. **Producer (Publisher)** - Application that sends messages
2. **Exchange** - Routes messages to queues based on routing rules
3. **Queue** - Buffer that stores messages until consumed
4. **Consumer** - Application that receives messages
5. **Binding** - Link between exchange and queue with routing key
6. **Routing Key** - Address that exchange uses to route messages
7. **Virtual Host (vhost)** - Logical grouping for isolation

### Message Flow

```
1. Producer publishes message to Exchange with Routing Key
2. Exchange routes message to Queue(s) based on Binding rules
3. Message is stored in Queue until consumed
4. Consumer receives message from Queue
5. Consumer sends ACK (acknowledgement) to RabbitMQ
6. RabbitMQ deletes message from Queue
```

### Virtual Hosts (vhosts)

**Purpose:** Logical separation of resources (exchanges, queues, users).

**Use Cases:**
- **Multi-tenancy** - Isolate environments (dev, staging, prod)
- **Security** - Separate permissions per vhost
- **Organization** - Group resources by team or application

**Example:**
```
/prod - Production environment
/staging - Staging environment
/dev - Development environment
/team-alpha - Team Alpha's queues
```

**CLI:**
```bash
# Create vhost
rabbitmqctl add_vhost /prod

# List vhosts
rabbitmqctl list_vhosts

# Set permissions
rabbitmqctl set_permissions -p /prod myuser ".*" ".*" ".*"
```

---

## Exchange Types

Exchanges route messages to queues based on **routing keys** and **bindings**.

### 1. Direct Exchange

**Routing:** Message delivered to queues whose **binding key exactly matches** the routing key.

**Use Case:** Unicast routing, point-to-point messaging.

**Example:**

```
Exchange: logs.direct
Binding: Queue "error_logs" -> routing key "error"
Binding: Queue "info_logs" -> routing key "info"

Publish with key "error" -> routed to "error_logs"
Publish with key "info" -> routed to "info_logs"
```

**Python Example:**

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# Declare exchange
channel.exchange_declare(exchange='logs.direct', exchange_type='direct')

# Declare queues
channel.queue_declare(queue='error_logs')
channel.queue_declare(queue='info_logs')

# Bind queues
channel.queue_bind(exchange='logs.direct', queue='error_logs', routing_key='error')
channel.queue_bind(exchange='logs.direct', queue='info_logs', routing_key='info')

# Publish message
channel.basic_publish(exchange='logs.direct', routing_key='error', body='Critical error!')
```

### 2. Fanout Exchange

**Routing:** Message delivered to **all queues** bound to the exchange (ignores routing key).

**Use Case:** Broadcast messaging, pub/sub pattern.

**Example:**

```
Exchange: notifications.fanout
Binding: Queue "email_queue"
Binding: Queue "sms_queue"
Binding: Queue "push_queue"

Publish message -> routed to ALL three queues
```

**Python Example:**

```python
# Declare fanout exchange
channel.exchange_declare(exchange='notifications.fanout', exchange_type='fanout')

# Bind multiple queues
channel.queue_bind(exchange='notifications.fanout', queue='email_queue')
channel.queue_bind(exchange='notifications.fanout', queue='sms_queue')
channel.queue_bind(exchange='notifications.fanout', queue='push_queue')

# Publish once, delivered to all queues
channel.basic_publish(exchange='notifications.fanout', routing_key='', body='New order!')
```

### 3. Topic Exchange

**Routing:** Message delivered to queues whose **binding pattern matches** the routing key.

**Patterns:**
- `*` - Matches exactly one word
- `#` - Matches zero or more words

**Use Case:** Multi-criteria routing, hierarchical messaging.

**Example:**

```
Exchange: events.topic
Binding: Queue "all_orders" -> pattern "order.*"
Binding: Queue "us_orders" -> pattern "order.us.#"
Binding: Queue "critical_only" -> pattern "*.*.critical"

Publish "order.us.created" -> routed to "all_orders", "us_orders"
Publish "order.eu.critical" -> routed to "all_orders", "critical_only"
Publish "payment.us.critical" -> routed to "critical_only"
```

**Python Example:**

```python
# Declare topic exchange
channel.exchange_declare(exchange='events.topic', exchange_type='topic')

# Bind queues with patterns
channel.queue_bind(exchange='events.topic', queue='all_orders', routing_key='order.*')
channel.queue_bind(exchange='events.topic', queue='us_orders', routing_key='order.us.#')
channel.queue_bind(exchange='events.topic', queue='critical_only', routing_key='*.*.critical')

# Publish with routing keys
channel.basic_publish(exchange='events.topic', routing_key='order.us.created', body='Order #123')
channel.basic_publish(exchange='events.topic', routing_key='order.eu.critical', body='Critical order!')
```

### 4. Headers Exchange

**Routing:** Match based on **message headers** instead of routing key.

**Use Case:** Complex routing with multiple attributes.

**Example:**

```python
# Declare headers exchange
channel.exchange_declare(exchange='tasks.headers', exchange_type='headers')

# Bind queue with header matching
channel.queue_bind(
exchange='tasks.headers',
queue='high_priority',
arguments={
'x-match': 'all', # all headers must match
'priority': 'high',
'region': 'us-east'
}
)

# Publish with headers
channel.basic_publish(
exchange='tasks.headers',
routing_key='',
body='Urgent task',
properties=pika.BasicProperties(
headers={'priority': 'high', 'region': 'us-east'}
)
)
```

### Default Exchange

**Name:** `""` (empty string)

**Type:** Direct exchange

**Behavior:** Routes messages to queues whose name matches the routing key.

**Example:**

```python
# Publish to default exchange (directly to queue name)
channel.basic_publish(exchange='', routing_key='my_queue', body='Hello')
```

---

## Queue Configuration

### Queue Properties

| Property | Description | Default |
|----------|-------------|---------|
| **Durable** | Queue survives broker restart | False |
| **Exclusive** | Used by only one connection, deleted when closed | False |
| **Auto-delete** | Deleted when last consumer unsubscribes | False |
| **TTL** | Message time-to-live (milliseconds) | None |
| **Max Length** | Maximum messages in queue | None |
| **Max Priority** | Enable priority queuing (0-255) | None |

### Durable Queues

**Purpose:** Persist queue metadata across broker restarts.

```python
channel.queue_declare(queue='tasks', durable=True)
```

**Note:** Durable queues require **persistent messages** to survive restart.

### Persistent Messages

**Purpose:** Messages survive broker restart.

```python
channel.basic_publish(
exchange='',
routing_key='tasks',
body='Task data',
properties=pika.BasicProperties(
delivery_mode=2, # Persistent
)
)
```

### Message TTL (Time-To-Live)

**Per-Queue TTL:**

```python
channel.queue_declare(
queue='short_lived',
arguments={'x-message-ttl': 60000} # 60 seconds
)
```

**Per-Message TTL:**

```python
channel.basic_publish(
exchange='',
routing_key='tasks',
body='Task data',
properties=pika.BasicProperties(
expiration='60000' # 60 seconds
)
)
```

### Queue Length Limit

**Purpose:** Prevent unbounded queue growth.

```python
channel.queue_declare(
queue='bounded_queue',
arguments={
'x-max-length': 10000,
'x-overflow': 'drop-head' # Drop oldest messages
}
)
```

**Overflow Behaviors:**
- `drop-head` - Drop oldest messages
- `reject-publish` - Reject new messages
- `reject-publish-dlx` - Reject and send to Dead Letter Exchange

### Priority Queues

**Purpose:** Process high-priority messages first.

```python
# Declare priority queue
channel.queue_declare(
queue='priority_tasks',
arguments={'x-max-priority': 10}
)

# Publish with priority
channel.basic_publish(
exchange='',
routing_key='priority_tasks',
body='Urgent task',
properties=pika.BasicProperties(priority=9)
)
```

### Dead Letter Exchange (DLX)

**Purpose:** Route rejected or expired messages to another exchange.

```python
channel.queue_declare(
queue='main_queue',
arguments={
'x-dead-letter-exchange': 'dlx.exchange',
'x-dead-letter-routing-key': 'failed'
}
)
```

**Use Cases:**
- Retry logic (reprocess failed messages)
- Error handling (log rejected messages)
- Delayed processing (TTL + DLX)

---

## Message Routing Patterns

### 1. Work Queue (Task Distribution)

**Pattern:** Distribute tasks among multiple workers.

**Configuration:**
- One queue, multiple consumers
- Round-robin distribution by default
- Use `prefetch_count` for fair dispatch

**Example:**

```python
# Producer
channel.queue_declare(queue='tasks', durable=True)
channel.basic_publish(
exchange='',
routing_key='tasks',
body='Process order #123',
properties=pika.BasicProperties(delivery_mode=2)
)

# Consumer (multiple instances)
def callback(ch, method, properties, body):
print(f"Processing: {body}")
# Simulate work
time.sleep(5)
ch.basic_ack(delivery_tag=method.delivery_tag)

channel.basic_qos(prefetch_count=1) # Fair dispatch
channel.basic_consume(queue='tasks', on_message_callback=callback)
channel.start_consuming()
```

### 2. Publish/Subscribe (Fanout)

**Pattern:** Broadcast messages to all subscribers.

**Configuration:**
- Fanout exchange
- Each subscriber has its own queue

**Example:**

```python
# Publisher
channel.exchange_declare(exchange='logs', exchange_type='fanout')
channel.basic_publish(exchange='logs', routing_key='', body='Log message')

# Subscriber 1
channel.queue_declare(queue='', exclusive=True)
result = channel.queue_declare(queue='', exclusive=True)
queue_name = result.method.queue
channel.queue_bind(exchange='logs', queue=queue_name)
```

### 3. Routing (Direct Exchange)

**Pattern:** Route messages based on severity/category.

**Example:**

```python
# Route error logs to error queue, info logs to info queue
channel.exchange_declare(exchange='logs.direct', exchange_type='direct')

# Bind queues
channel.queue_bind(exchange='logs.direct', queue='error_logs', routing_key='error')
channel.queue_bind(exchange='logs.direct', queue='info_logs', routing_key='info')

# Publish
channel.basic_publish(exchange='logs.direct', routing_key='error', body='Critical error!')
```

### 4. Topics (Pattern Matching)

**Pattern:** Route based on multi-criteria (e.g., `region.service.level`).

**Example:**

```python
# Routing: <region>.<service>.<level>
# us.orders.critical -> US critical orders
# eu.*.critical -> All EU critical messages

channel.exchange_declare(exchange='events.topic', exchange_type='topic')

# Bind patterns
channel.queue_bind(exchange='events.topic', queue='us_critical', routing_key='us.*.critical')
channel.queue_bind(exchange='events.topic', queue='eu_all', routing_key='eu.#')
```

### 5. RPC (Request/Reply)

**Pattern:** Synchronous request/response using correlation ID.

**Request:**

```python
import uuid

# Create callback queue for replies
result = channel.queue_declare(queue='', exclusive=True)
callback_queue = result.method.queue

correlation_id = str(uuid.uuid4())

channel.basic_publish(
exchange='',
routing_key='rpc_queue',
properties=pika.BasicProperties(
reply_to=callback_queue,
correlation_id=correlation_id,
),
body='Calculate 42'
)

# Wait for reply with matching correlation_id
```

**Response:**

```python
def on_request(ch, method, props, body):
response = f"Result: {body}"

ch.basic_publish(
exchange='',
routing_key=props.reply_to,
properties=pika.BasicProperties(correlation_id=props.correlation_id),
body=response
)
ch.basic_ack(delivery_tag=method.delivery_tag)
```

---

## Publisher-Consumer Patterns

### Publisher Confirms

**Purpose:** Ensure messages are successfully received by broker.

**Configuration:**

```python
channel.confirm_delivery()

try:
channel.basic_publish(exchange='', routing_key='tasks', body='Task data')
print("Message confirmed by broker")
except pika.exceptions.UnroutableError:
print("Message could not be routed")
```

### Consumer Acknowledgements

**Manual ACK (Recommended):**

```python
def callback(ch, method, properties, body):
try:
process_message(body)
ch.basic_ack(delivery_tag=method.delivery_tag)
except Exception as e:
ch.basic_nack(delivery_tag=method.delivery_tag, requeue=True)

channel.basic_consume(queue='tasks', on_message_callback=callback, auto_ack=False)
```

**Auto ACK (Not Recommended):**

```python
channel.basic_consume(queue='tasks', on_message_callback=callback, auto_ack=True)
```

### Prefetch Count (QoS)

**Purpose:** Control how many unacknowledged messages a consumer can have.

```python
channel.basic_qos(prefetch_count=1) # Process one message at a time
```

**Use Case:** Fair dispatch in work queues.

### Consumer Cancellation

**Graceful Shutdown:**

```python
def callback(ch, method, properties, body):
if stop_signal:
ch.basic_cancel(consumer_tag)
ch.stop_consuming()
```

---

## High Availability & Clustering

### Clustering

**Purpose:** Distribute load and provide fault tolerance.

**Architecture:**

```

Node 1 Node 2 Node 3 
rabbit@n1 rabbit@n2 rabbit@n3 

```

**Features:**
- **Metadata replication** - Exchanges, queues, bindings replicated
- **Message distribution** - Messages NOT replicated by default (use quorum queues)
- **Load balancing** - Clients connect to any node

**Setup:**

```bash
# On each node, join cluster
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl join_cluster rabbit@node1
rabbitmqctl start_app

# Verify cluster status
rabbitmqctl cluster_status
```

### Quorum Queues

**Purpose:** Replicated queues for high availability.

**Features:**
- **Raft consensus** - Leader election, log replication
- **Data replication** - Messages replicated to majority of nodes
- **Automatic failover** - New leader elected if current fails

**Configuration:**

```python
channel.queue_declare(
queue='ha_queue',
durable=True,
arguments={'x-queue-type': 'quorum'}
)
```

**Recommendation:** Use quorum queues for critical workloads.

### Mirrored Queues (Classic HA)

**Note:** Deprecated in favor of quorum queues.

**Configuration:**

```bash
rabbitmqctl set_policy ha-all "^ha\." '{"ha-mode":"all"}'
```

### Load Balancing

**Options:**

1. **HAProxy** - Layer 4 load balancer
2. **Nginx** - HTTP/AMQP load balancing
3. **Kubernetes Service** - ClusterIP with multiple replicas

**HAProxy Example:**

```
frontend rabbitmq_amqp
bind *:5672
mode tcp
default_backend rabbitmq_nodes

backend rabbitmq_nodes
mode tcp
balance roundrobin
server rabbit1 rabbit1:5672 check
server rabbit2 rabbit2:5672 check
server rabbit3 rabbit3:5672 check
```

---

## Federation & Shovel

### Federation

**Purpose:** Connect RabbitMQ clusters across WAN/regions.

**Use Cases:**
- Multi-datacenter deployments
- Geo-distributed applications
- Cloud to on-premise bridging

**Setup:**

```bash
# Enable federation plugin
rabbitmq-plugins enable rabbitmq_federation
rabbitmq-plugins enable rabbitmq_federation_management

# Define upstream
rabbitmqctl set_parameter federation-upstream upstream1 \
'{"uri":"amqp://remote-host","ack-mode":"on-confirm"}'

# Apply policy
rabbitmqctl set_policy federate-exchanges "^federated\." \
'{"federation-upstream-set":"all"}'
```

### Shovel

**Purpose:** Move messages between queues/exchanges (same or different clusters).

**Use Cases:**
- Migration between clusters
- Forwarding to backup cluster
- Aggregating from multiple sources

**Setup:**

```bash
# Enable shovel plugin
rabbitmq-plugins enable rabbitmq_shovel
rabbitmq-plugins enable rabbitmq_shovel_management

# Create shovel
rabbitmqctl set_parameter shovel my-shovel \
'{"src-uri":"amqp://source","src-queue":"source_queue",
"dest-uri":"amqp://dest","dest-queue":"dest_queue"}'
```

---

## Security & Authentication

### User Management

```bash
# Add user
rabbitmqctl add_user myuser mypassword

# Set tags (administrator, monitoring, management)
rabbitmqctl set_user_tags myuser administrator

# Set permissions (configure, write, read)
rabbitmqctl set_permissions -p / myuser ".*" ".*" ".*"

# List users
rabbitmqctl list_users
```

### Virtual Host Permissions

```bash
# Create vhost
rabbitmqctl add_vhost /production

# Set permissions per vhost
rabbitmqctl set_permissions -p /production myuser "^prod-.*" "^prod-.*" "^prod-.*"
```

### TLS/SSL

**Configuration (`rabbitmq.conf`):**

```ini
listeners.ssl.default = 5671

ssl_options.cacertfile = /path/to/ca_certificate.pem
ssl_options.certfile = /path/to/server_certificate.pem
ssl_options.keyfile = /path/to/server_key.pem
ssl_options.verify = verify_peer
ssl_options.fail_if_no_peer_cert = true
```

**Client Connection:**

```python
import ssl

ssl_context = ssl.create_default_context(cafile="/path/to/ca_cert.pem")
ssl_context.load_cert_chain("/path/to/client_cert.pem", "/path/to/client_key.pem")

parameters = pika.ConnectionParameters(
host='rabbitmq.example.com',
port=5671,
ssl_options=pika.SSLOptions(ssl_context)
)
connection = pika.BlockingConnection(parameters)
```

### LDAP/OAuth2 Authentication

**LDAP Plugin:**

```bash
rabbitmq-plugins enable rabbitmq_auth_backend_ldap
```

**Configuration:**

```ini
auth_backends.1 = ldap
auth_ldap.servers.1 = ldap.example.com
auth_ldap.user_dn_pattern = cn=${username},ou=users,dc=example,dc=com
```

---

## Monitoring & Management

### Management UI

**Enable Plugin:**

```bash
rabbitmq-plugins enable rabbitmq_management
```

**Access:** `http://localhost:15672`

**Features:**
- Queue/exchange overview
- Message rates, publish/consume stats
- Connection/channel monitoring
- User/permission management
- Policy configuration

### CLI Tools

```bash
# List queues
rabbitmqctl list_queues name messages consumers

# List exchanges
rabbitmqctl list_exchanges name type

# List bindings
rabbitmqctl list_bindings

# Node status
rabbitmqctl status

# Cluster status
rabbitmqctl cluster_status

# Memory usage
rabbitmqctl status | grep memory
```

### Prometheus Exporter

**Enable Plugin:**

```bash
rabbitmq-plugins enable rabbitmq_prometheus
```

**Scrape Endpoint:** `http://localhost:15692/metrics`

**Key Metrics:**
- `rabbitmq_queue_messages` - Messages in queue
- `rabbitmq_queue_messages_ready` - Ready messages
- `rabbitmq_queue_messages_unacknowledged` - Unacked messages
- `rabbitmq_connections` - Active connections
- `rabbitmq_consumers` - Active consumers
- `rabbitmq_channel_messages_published_total` - Published messages

**Prometheus Config:**

```yaml
scrape_configs:
- job_name: 'rabbitmq'
static_configs:
- targets: ['rabbitmq:15692']
```

### Health Checks

**Aliveness Test:**

```bash
curl -u guest:guest http://localhost:15672/api/aliveness-test/%2F
```

**Node Health:**

```bash
rabbitmq-diagnostics ping
rabbitmq-diagnostics node_health_check
```

---

## Client Libraries

### Python (pika)

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')
channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
connection.close()
```

### Go (amqp091-go)

```go
import (
amqp "github.com/rabbitmq/amqp091-go"
)

conn, _ := amqp.Dial("amqp://guest:guest@localhost:5672/")
ch, _ := conn.Channel()

q, _ := ch.QueueDeclare("hello", false, false, false, false, nil)
ch.Publish("", q.Name, false, false, amqp.Publishing{
ContentType: "text/plain",
Body: []byte("Hello World"),
})
```

### Java (Spring AMQP)

```java
@Configuration
public class RabbitConfig {
@Bean
public Queue queue() {
return new Queue("hello");
}

@Bean
public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
return new RabbitTemplate(connectionFactory);
}
}

// Publish
rabbitTemplate.convertAndSend("hello", "Hello World");
```

### Node.js (amqplib)

```javascript
const amqp = require('amqplib');

const connection = await amqp.connect('amqp://localhost');
const channel = await connection.createChannel();

await channel.assertQueue('hello');
channel.sendToQueue('hello', Buffer.from('Hello World'));
```

---

## Kubernetes Deployment

### Helm Chart (Bitnami)

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install rabbitmq bitnami/rabbitmq \
--set auth.username=admin \
--set auth.password=secretpassword \
--set replicaCount=3 \
--set clustering.enabled=true \
--set persistence.enabled=true \
--set persistence.size=8Gi
```

### StatefulSet Example

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
name: rabbitmq
spec:
serviceName: rabbitmq
replicas: 3
selector:
matchLabels:
app: rabbitmq
template:
metadata:
labels:
app: rabbitmq
spec:
containers:
- name: rabbitmq
image: rabbitmq:3.13-management
ports:
- containerPort: 5672
name: amqp
- containerPort: 15672
name: management
env:
- name: RABBITMQ_ERLANG_COOKIE
value: "secret-cookie"
- name: RABBITMQ_DEFAULT_USER
value: "admin"
- name: RABBITMQ_DEFAULT_PASS
valueFrom:
secretKeyRef:
name: rabbitmq-secret
key: password
volumeMounts:
- name: data
mountPath: /var/lib/rabbitmq
volumeClaimTemplates:
- metadata:
name: data
spec:
accessModes: [ "ReadWriteOnce" ]
resources:
requests:
storage: 8Gi
---
apiVersion: v1
kind: Service
metadata:
name: rabbitmq
spec:
selector:
app: rabbitmq
ports:
- name: amqp
port: 5672
- name: management
port: 15672
clusterIP: None # Headless service for StatefulSet
```

### RabbitMQ Cluster Operator

**Install Operator:**

```bash
kubectl apply -f https://github.com/rabbitmq/cluster-operator/releases/latest/download/cluster-operator.yml
```

**RabbitMQ Cluster Custom Resource:**

```yaml
apiVersion: rabbitmq.com/v1beta1
kind: RabbitmqCluster
metadata:
name: rabbitmq-cluster
spec:
replicas: 3
image: rabbitmq:3.13-management
persistence:
storageClassName: standard
storage: 10Gi
resources:
requests:
cpu: 500m
memory: 1Gi
limits:
cpu: 1
memory: 2Gi
rabbitmq:
additionalConfig: |
cluster_formation.peer_discovery_backend = rabbit_peer_discovery_k8s
cluster_formation.k8s.host = kubernetes.default.svc.cluster.local
cluster_formation.k8s.address_type = hostname
```

---

## Performance Tuning

### Message Throughput

**Batching:**

```python
# Publish in batches
for i in range(1000):
channel.basic_publish(exchange='', routing_key='queue', body=f'Message {i}')
# Commit batch
channel.tx_commit()
```

**Async Publishing:**

```python
import pika
from pika.adapters.asyncio_connection import AsyncioConnection

async def publish():
connection = await AsyncioConnection.create(parameters)
channel = await connection.channel()
for i in range(10000):
await channel.basic_publish(exchange='', routing_key='queue', body=f'Msg {i}')
```

### Consumer Performance

**Prefetch Count:**

```python
channel.basic_qos(prefetch_count=10) # Process up to 10 messages concurrently
```

**Multiple Consumers:**

```python
# Scale horizontally - run multiple consumer processes
```

### Queue Performance

**Lazy Queues (Disk-backed):**

```python
channel.queue_declare(
queue='large_queue',
arguments={'x-queue-mode': 'lazy'}
)
```

**Use Case:** Millions of messages, low memory footprint.

### Memory Management

**Configuration (`rabbitmq.conf`):**

```ini
# Memory threshold (40% of total RAM)
vm_memory_high_watermark.relative = 0.4

# Paging threshold (50% of memory limit)
vm_memory_high_watermark_paging_ratio = 0.5

# Disk free space limit (50GB)
disk_free_limit.absolute = 50GB
```

---

## Best Practices

### 1. Use Durable Queues & Persistent Messages

**Why:** Survive broker restarts.

```python
channel.queue_declare(queue='tasks', durable=True)
channel.basic_publish(
exchange='',
routing_key='tasks',
body='Task data',
properties=pika.BasicProperties(delivery_mode=2)
)
```

### 2. Enable Publisher Confirms

**Why:** Ensure messages are received by broker.

```python
channel.confirm_delivery()
```

### 3. Use Manual Acknowledgements

**Why:** Prevent message loss on consumer failures.

```python
channel.basic_consume(queue='tasks', on_message_callback=callback, auto_ack=False)
```

### 4. Set Prefetch Count

**Why:** Fair dispatch in work queues.

```python
channel.basic_qos(prefetch_count=1)
```

### 5. Use Dead Letter Exchanges

**Why:** Handle failed messages gracefully.

```python
channel.queue_declare(
queue='main_queue',
arguments={
'x-dead-letter-exchange': 'dlx',
'x-dead-letter-routing-key': 'failed'
}
)
```

### 6. Monitor Queue Lengths

**Why:** Detect backpressure and consumer failures.

**Alert on:**
- Queue length > threshold (e.g., 10,000 messages)
- Message age > threshold (e.g., 5 minutes)

### 7. Use Quorum Queues for HA

**Why:** Data replication and automatic failover.

```python
channel.queue_declare(
queue='critical_queue',
arguments={'x-queue-type': 'quorum'}
)
```

### 8. Set Message TTL for Temporary Data

**Why:** Prevent unbounded growth of stale messages.

```python
channel.queue_declare(
queue='temp_queue',
arguments={'x-message-ttl': 300000} # 5 minutes
)
```

### 9. Use Virtual Hosts for Isolation

**Why:** Separate environments, teams, or applications.

```bash
rabbitmqctl add_vhost /production
rabbitmqctl add_vhost /staging
```

### 10. Enable TLS in Production

**Why:** Encrypt data in transit.

```ini
listeners.ssl.default = 5671
ssl_options.cacertfile = /path/to/ca.pem
ssl_options.certfile = /path/to/cert.pem
ssl_options.keyfile = /path/to/key.pem
```

---

## Troubleshooting

### Issue 1: Messages Not Being Consumed

**Symptoms:**
- Messages piling up in queue
- Consumers connected but not receiving

**Debugging:**

1. **Check consumer status:**
```bash
rabbitmqctl list_queues name messages consumers
```

2. **Verify consumer prefetch:**
```python
channel.basic_qos(prefetch_count=1) # Ensure set
```

3. **Check for unacknowledged messages:**
```bash
rabbitmqctl list_queues name messages_unacknowledged
```

4. **Restart consumers with manual ACK:**
```python
def callback(ch, method, properties, body):
process(body)
ch.basic_ack(delivery_tag=method.delivery_tag)
```

### Issue 2: High Memory Usage

**Symptoms:**
- RabbitMQ using excessive memory
- Broker triggers memory alarm

**Solutions:**

1. **Enable lazy queues:**
```python
channel.queue_declare(queue='large', arguments={'x-queue-mode': 'lazy'})
```

2. **Reduce prefetch count:**
```python
channel.basic_qos(prefetch_count=10) # Lower value
```

3. **Purge old messages:**
```bash
rabbitmqctl purge_queue my_queue
```

4. **Increase memory threshold:**
```ini
vm_memory_high_watermark.relative = 0.6
```

### Issue 3: Connection Refused

**Symptoms:**
- Clients cannot connect
- "Connection refused" errors

**Solutions:**

1. **Verify RabbitMQ is running:**
```bash
systemctl status rabbitmq-server
```

2. **Check port bindings:**
```bash
netstat -tuln | grep 5672
```

3. **Check firewall rules:**
```bash
firewall-cmd --list-ports
```

4. **Verify user credentials:**
```bash
rabbitmqctl authenticate_user myuser mypassword
```

### Issue 4: Cluster Partition

**Symptoms:**
- Nodes not communicating
- Split-brain scenario

**Detection:**

```bash
rabbitmqctl cluster_status
# Look for "partitions" field
```

**Resolution:**

```bash
# Stop minority partition nodes
rabbitmqctl stop_app

# Reset node
rabbitmqctl reset

# Rejoin cluster
rabbitmqctl join_cluster rabbit@primary_node
rabbitmqctl start_app
```

### Issue 5: Slow Message Processing

**Symptoms:**
- High latency between publish and consume
- Messages delayed

**Solutions:**

1. **Check consumer count:**
```bash
rabbitmqctl list_queues name consumers
# Add more consumers if needed
```

2. **Increase prefetch count:**
```python
channel.basic_qos(prefetch_count=50)
```

3. **Use multiple channels:**
```python
# Create multiple channels per connection
channel1 = connection.channel()
channel2 = connection.channel()
```

4. **Profile consumer code:**
```python
import time
start = time.time()
process_message(body)
print(f"Processing took {time.time() - start}s")
```

---

## Company / Platform Specific Examples

### Platform Event Processing

**Use Case:** Process Tekton pipeline events asynchronously.

**Architecture:**

```
Tekton Event → RabbitMQ Exchange (topic) → Queues (notification, metrics, audit)
```

**Configuration:**

```python
# Declare topic exchange
channel.exchange_declare(exchange='platform.events', exchange_type='topic')

# Bind queues
channel.queue_bind(exchange='platform.events', queue='notifications', routing_key='pipeline.*.failed')
channel.queue_bind(exchange='platform.events', queue='metrics', routing_key='pipeline.#')
channel.queue_bind(exchange='platform.events', queue='audit', routing_key='#')

# Publish pipeline event
channel.basic_publish(
exchange='platform.events',
routing_key='pipeline.build.failed',
body=json.dumps({
'pipeline': 'my-app-build',
'status': 'failed',
'timestamp': '2026-03-19T10:00:00Z'
}),
properties=pika.BasicProperties(delivery_mode=2)
)
```

**Consumer (Notification Service):**

```python
def on_failed_pipeline(ch, method, properties, body):
event = json.loads(body)
send_slack_notification(f"Pipeline {event['pipeline']} failed!")
ch.basic_ack(delivery_tag=method.delivery_tag)

channel.basic_qos(prefetch_count=5)
channel.basic_consume(queue='notifications', on_message_callback=on_failed_pipeline)
channel.start_consuming()
```

---

## References

- **RabbitMQ Documentation:** https://www.rabbitmq.com/documentation.html
- **AMQP 0-9-1 Reference:** https://www.rabbitmq.com/amqp-0-9-1-reference.html
- **Management HTTP API:** https://www.rabbitmq.com/management.html
- **Clustering Guide:** https://www.rabbitmq.com/clustering.html
- **Production Checklist:** https://www.rabbitmq.com/production-checklist.html
- **Kubernetes Operator:** https://www.rabbitmq.com/kubernetes/operator/operator-overview.html

---

**Last Updated:** 2026-03-19
**Author:** Documentation Team (Company Infrastructure Team)
**License:** CC BY-SA 4.0
