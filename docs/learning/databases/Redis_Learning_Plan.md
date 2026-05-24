# Redis Learning Plan - Practical Guide

**Created:** 2026-03-09
**Author:** Documentation Team
**Goal:** Learn Redis fundamentals with practical examples
**Duration:** 8-12 hours (self-paced)

---

## Learning Structure

```
1. Basics (2 hours) → Redis installation, basic commands
2. Data Structures (3 hours) → Practical examples for each type
3. Real-world examples (3 hours) → Cache, session, rate limiting
4. Kubernetes integration (2 hours) → Redis in K8s
5. Project (2+ hours) → Your own mini-application
```

---

## 1. Basics - Redis Setup and First Steps

### 1.1 Installation (10 minutes)

**With Docker (simplest):**
```bash
# Start Redis in Docker
docker run -d --name redis-learning \
 -p 6379:6379 \
 redis:7-alpine

# Verification
docker ps | grep redis-learning

# Access Redis CLI
docker exec -it redis-learning redis-cli
```

**Native on Fedora:**
```bash
sudo dnf install redis
sudo systemctl start redis
sudo systemctl enable redis

# Connect
redis-cli
```

### 1.2 First Commands (20 minutes)

**Exercise: Redis CLI basics**

```bash
# Test connection
redis-cli ping
# Response: PONG

# In Redis CLI:
redis-cli

# Simple key-value operations
SET name "Miklos"
GET name
# Response: "Miklos"

# Delete key
DEL name

# Check if key exists
EXISTS name
# Response: 0 (does not exist)

# List all keys (ONLY in development!)
KEYS *

# Key expiration (TTL - Time To Live)
SET session:user123 "active"
EXPIRE session:user123 3600 # Expires in 1 hour
TTL session:user123 # Returns remaining seconds

# Set key with expiration immediately
SETEX cache:user123 300 "cached_data" # 5 minutes
```

**Task 1:** Try the commands and observe the TTL countdown!

### 1.3 Python Client (30 minutes)

**Install Python Redis library:**
```bash
pip install redis
```

**First Python script:**

Create a `redis_basics.py` file:

```python
#!/usr/bin/env python3
import redis
import time

# Connect to Redis
r = redis.Redis(
 host='localhost',
 port=6379,
 db=0,
 decode_responses=True # Returns values as strings
)

# Test connection
try:
 r.ping()
 print(" Redis connection OK!")
except redis.ConnectionError:
 print(" Redis not available!")
 exit(1)

# Basic operations
r.set('greeting', 'Hello Redis!')
print(f"Value: {r.get('greeting')}")

# Set expiration time
r.setex('temp_key', 10, 'This disappears in 10 seconds')
print(f"TTL: {r.ttl('temp_key')} seconds")

time.sleep(5)
print(f"After 5s TTL: {r.ttl('temp_key')} seconds")

# Set multiple keys
r.mset({'user:1:name': 'Alice', 'user:2:name': 'Bob'})
print(f"Users: {r.mget(['user:1:name', 'user:2:name'])}")

# Counter (INCR)
r.set('page_views', 0)
r.incr('page_views')
r.incr('page_views')
r.incrby('page_views', 5)
print(f"Page views: {r.get('page_views')}") # 7

# Cleanup
r.flushdb() # WARNING: Deletes everything in the database!
```

**Run:**
```bash
python3 redis_basics.py
```

---

## 2. Data Structures with Practical Examples

### 2.1 Strings (already covered)

**Use case:** Simple cache, configuration values

### 2.2 Lists - 30 minutes

**Use case:** Background task queue, storing recent items

**Exercise:**

Create a `redis_lists.py` file:

```python
import redis

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

# --- Message queue example (Queue) ---

# Add items to end of queue (producer)
r.rpush('task_queue', 'send_email_to_user1')
r.rpush('task_queue', 'process_payment_123')
r.rpush('task_queue', 'generate_report_456')

print(f"Queue size: {r.llen('task_queue')}")

# Process items from front of queue (consumer)
while r.llen('task_queue') > 0:
 task = r.lpop('task_queue')
 print(f"Processing: {task}")

# --- Recent activities (Recent Activity) ---

def add_activity(user_id, activity):
 key = f'user:{user_id}:activities'
 r.lpush(key, activity) # Insert at beginning (newest first)
 r.ltrim(key, 0, 9) # Keep only last 10 items
 r.expire(key, 86400) # Expire after 24 hours

# Simulation
add_activity(123, 'Login')
add_activity(123, 'Profile updated')
add_activity(123, 'Photo uploaded')

# Get last 5 activities
recent = r.lrange('user:123:activities', 0, 4)
print(f"\nUser 123 recent activities:")
for activity in recent:
 print(f" - {activity}")
```

**Task 2:** Modify the code to store 20 activities and display only the last 3!

### 2.3 Hashes - 30 minutes

**Use case:** Storing objects (e.g., user profile)

**Exercise:**

Create a `redis_hashes.py` file:

```python
import redis
import json

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

# --- User profile storage ---

user_id = 42

# Set hash (object fields)
r.hset(f'user:{user_id}', mapping={
 'name': 'Documentation Team',
 'email': 'developer@company.com',
 'role': 'SRE',
 'last_login': '2026-03-09 10:30:00'
})

# Get one field
name = r.hget(f'user:{user_id}', 'name')
print(f"Name: {name}")

# Get all fields
user_data = r.hgetall(f'user:{user_id}')
print(f"\nFull profile: {user_data}")

# Update one field
r.hset(f'user:{user_id}', 'last_login', '2026-03-09 14:00:00')

# Counter in a field
r.hincrby(f'user:{user_id}', 'login_count', 1)
r.hincrby(f'user:{user_id}', 'login_count', 1)

print(f"Login count: {r.hget(f'user:{user_id}', 'login_count')}")

# --- Comparison with JSON string ---

# String-based storage (NOT recommended!)
user_json = json.dumps({'name': 'Test', 'email': 'test@test.com'})
r.set('user:json:99', user_json)
data = json.loads(r.get('user:json:99'))
print(f"\nJSON-based storage: {data}")

# Hash-based storage (RECOMMENDED!)
# Advantages:
# - Individual fields can be modified without loading/saving entire object
# - More memory efficient
# - Faster field access
```

**Task 3:** Create a function that increments `login_count` and updates `last_login` timestamp!

### 2.4 Sets - 30 minutes

**Use case:** Unique items, membership checks, intersection/union operations

**Exercise:**

Create a `redis_sets.py` file:

```python
import redis

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

# --- Track online users ---

def user_online(user_id):
 r.sadd('online_users', user_id)
 r.expire('online_users', 300) # Deleted after 5 minutes of inactivity

def user_offline(user_id):
 r.srem('online_users', user_id)

def is_user_online(user_id):
 return r.sismember('online_users', user_id)

# Simulation
user_online('user123')
user_online('user456')
user_online('user789')

print(f"Online users count: {r.scard('online_users')}")
print(f"user123 online? {is_user_online('user123')}")
print(f"All online: {r.smembers('online_users')}")

# --- Tags and intersections ---

# Articles with tags
r.sadd('article:1:tags', 'python', 'redis', 'tutorial')
r.sadd('article:2:tags', 'python', 'kubernetes', 'devops')
r.sadd('article:3:tags', 'redis', 'performance', 'optimization')

# Common tags (intersection)
common_tags = r.sinter('article:1:tags', 'article:2:tags')
print(f"\nCommon tags between article:1 and article:2: {common_tags}")

# All tags (union)
all_tags = r.sunion('article:1:tags', 'article:2:tags', 'article:3:tags')
print(f"All unique tags: {all_tags}")

# Difference
unique_to_1 = r.sdiff('article:1:tags', 'article:2:tags')
print(f"Only in article:1: {unique_to_1}")
```

**Task 4:** Create a function that finds all articles with a given tag!

### 2.5 Sorted Sets - 30 minutes

**Use case:** Leaderboard, priority queue, time-series events

**Exercise:**

Create a `redis_sorted_sets.py` file:

```python
import redis
import time

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

# --- Player leaderboard ---

def update_score(player, score):
 r.zadd('leaderboard', {player: score})

# Add scores
update_score('Alice', 1500)
update_score('Bob', 2000)
update_score('Charlie', 1800)
update_score('Dave', 2200)
update_score('Eve', 1900)

# Top 3 players (highest score)
top_3 = r.zrevrange('leaderboard', 0, 2, withscores=True)
print(" Top 3 players:")
for rank, (player, score) in enumerate(top_3, 1):
 print(f" {rank}. {player}: {int(score)} points")

# A player's rank (0-indexed)
bob_rank = r.zrevrank('leaderboard', 'Bob')
print(f"\nBob's rank: {bob_rank + 1}") # +1 to start from 1

# A player's score
bob_score = r.zscore('leaderboard', 'Bob')
print(f"Bob's points: {int(bob_score)}")

# Increment score
r.zincrby('leaderboard', 300, 'Alice') # Alice gets 300 points
print(f"\nAlice's new score: {int(r.zscore('leaderboard', 'Alice'))}")

# --- Time-based events (timestamp score) ---

# Add events (score = unix timestamp)
current_time = time.time()
r.zadd('events', {
 'user_login': current_time - 3600, # 1 hour ago
 'file_upload': current_time - 1800, # 30 minutes ago
 'api_call': current_time - 300, # 5 minutes ago
 'error_occurred': current_time - 60 # 1 minute ago
})

# Events in last 1 hour
one_hour_ago = current_time - 3600
recent_events = r.zrangebyscore('events', one_hour_ago, current_time, withscores=True)
print("\nEvents in last 1 hour:")
for event, timestamp in recent_events:
 print(f" {event}: {int(current_time - timestamp)} seconds ago")

# Delete old events (older than 1 day)
one_day_ago = current_time - 86400
r.zremrangebyscore('events', '-inf', one_day_ago)
```

**Task 5:** Create a function that returns a player's position among all players!

---

## 3. Real-World Examples

### 3.1 Cache Implementation (45 minutes)

**Exercise: Database cache**

Create a `redis_cache_example.py` file:

```python
import redis
import json
import time
import hashlib

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

# Simulate slow database
def slow_database_query(user_id):
 """Simulates a slow DB query (500ms)"""
 time.sleep(0.5)
 return {
 'id': user_id,
 'name': f'User {user_id}',
 'email': f'user{user_id}@example.com',
 'role': 'developer'
 }

# Cached query
def get_user_cached(user_id, cache_ttl=300):
 """
 Get user from cache, or from DB if not cached.

 Args:
 user_id: User identifier
 cache_ttl: Cache expiration time in seconds (default: 5 minutes)
 """
 cache_key = f'user:{user_id}'

 # 1. Try to read from cache
 cached_data = r.get(cache_key)

 if cached_data:
 print(f" Cache HIT user:{user_id}")
 return json.loads(cached_data)

 # 2. If not cached, query database
 print(f" Cache MISS user:{user_id} - DB query...")
 user_data = slow_database_query(user_id)

 # 3. Store in cache
 r.setex(cache_key, cache_ttl, json.dumps(user_data))

 return user_data

# Test
print("=== Cache Demo ===\n")

# First request - from DB (slow)
start = time.time()
user = get_user_cached(42)
elapsed = time.time() - start
print(f"Response time: {elapsed:.3f}s")
print(f"User: {user}\n")

# Second request - from cache (fast)
start = time.time()
user = get_user_cached(42)
elapsed = time.time() - start
print(f"Response time: {elapsed:.3f}s")
print(f"User: {user}\n")

# --- Cache invalidation (deletion) ---
def invalidate_user_cache(user_id):
 """Delete cache when user is modified"""
 r.delete(f'user:{user_id}')
 print(f"Cache deleted: user:{user_id}")

invalidate_user_cache(42)

# Next request comes from DB again
get_user_cached(42)

# --- Cache function result with decorator ---
def cache_result(ttl=300):
 """Decorator to cache function results"""
 def decorator(func):
 def wrapper(*args, **kwargs):
 # Generate cache key from arguments
 key_data = f"{func.__name__}:{args}:{kwargs}"
 cache_key = f"cache:{hashlib.md5(key_data.encode()).hexdigest()}"

 # Cache lookup
 cached = r.get(cache_key)
 if cached:
 return json.loads(cached)

 # Calculate result
 result = func(*args, **kwargs)

 # Cache it
 r.setex(cache_key, ttl, json.dumps(result))
 return result

 return wrapper
 return decorator

@cache_result(ttl=60)
def expensive_calculation(n):
 """Simulates complex calculation"""
 time.sleep(1)
 return n ** 2

print("\n=== Decorator Demo ===\n")
start = time.time()
result = expensive_calculation(10)
print(f"Result: {result}, Time: {time.time() - start:.3f}s")

start = time.time()
result = expensive_calculation(10) # From cache
print(f"Result: {result}, Time: {time.time() - start:.3f}s")
```

**Task 6:** Modify the decorator to collect cache miss/hit statistics!

### 3.2 Session Store (30 minutes)

**Exercise: Flask web application session in Redis**

Install Flask:
```bash
pip install flask flask-session
```

Create a `redis_session_example.py` file:

```python
from flask import Flask, session, request, jsonify
from flask_session import Session
import redis

app = Flask(__name__)

# Redis session configuration
app.config['SECRET_KEY'] = 'super-secret-key-change-in-production'
app.config['SESSION_TYPE'] = 'redis'
app.config['SESSION_REDIS'] = redis.Redis(host='localhost', port=6379, db=0)
app.config['SESSION_PERMANENT'] = False
app.config['SESSION_USE_SIGNER'] = True

Session(app)

@app.route('/login', methods=['POST'])
def login():
 """User login"""
 username = request.json.get('username')
 session['username'] = username
 session['logged_in'] = True
 return jsonify({'message': f'Logged in: {username}'})

@app.route('/profile')
def profile():
 """Profile page - with session check"""
 if not session.get('logged_in'):
 return jsonify({'error': 'Not logged in'}), 401

 return jsonify({
 'username': session.get('username'),
 'message': 'This is your profile'
 })

@app.route('/logout', methods=['POST'])
def logout():
 """Logout"""
 session.clear()
 return jsonify({'message': 'Logged out'})

@app.route('/set/<key>/<value>')
def set_session(key, value):
 """Store arbitrary data in session"""
 session[key] = value
 return jsonify({'message': f'Session value set: {key}={value}'})

@app.route('/get/<key>')
def get_session(key):
 """Get session data"""
 value = session.get(key, 'No such key')
 return jsonify({key: value})

if __name__ == '__main__':
 print("\n=== Flask Session Demo ===")
 print("Try with curl or Postman:")
 print(" curl -X POST http://localhost:5000/login -H 'Content-Type: application/json' -d '{\"username\":\"miklos\"}' -c cookies.txt")
 print(" curl http://localhost:5000/profile -b cookies.txt")
 print(" curl http://localhost:5000/set/theme/dark -b cookies.txt")
 print(" curl http://localhost:5000/get/theme -b cookies.txt")
 print()
 app.run(debug=True)
```

**Run and test:**
```bash
# Start application
python3 redis_session_example.py

# In another terminal:
curl -X POST http://localhost:5000/login \
 -H 'Content-Type: application/json' \
 -d '{"username":"miklos"}' \
 -c cookies.txt

curl http://localhost:5000/profile -b cookies.txt

# Check session keys in Redis:
docker exec -it redis-learning redis-cli
KEYS session:*
```

### 3.3 Rate Limiting (30 minutes)

**Exercise: API rate limiter**

Create a `redis_rate_limiter.py` file:

```python
import redis
import time
from functools import wraps

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

class RateLimitExceeded(Exception):
 pass

def rate_limit(max_requests=10, window_seconds=60):
 """
 Rate limiter decorator.

 Args:
 max_requests: Maximum number of requests
 window_seconds: Time window in seconds
 """
 def decorator(func):
 @wraps(func)
 def wrapper(user_id, *args, **kwargs):
 key = f'rate_limit:{func.__name__}:{user_id}'

 # Current request count
 current = r.get(key)

 if current is None:
 # First request in this time window
 r.setex(key, window_seconds, 1)
 elif int(current) >= max_requests:
 # Limit exceeded
 ttl = r.ttl(key)
 raise RateLimitExceeded(
 f'Rate limit exceeded! Try again in {ttl} seconds.'
 )
 else:
 # Increment request counter
 r.incr(key)

 # Call original function
 return func(user_id, *args, **kwargs)

 return wrapper
 return decorator

# Sliding window rate limiter (more accurate)
def sliding_window_rate_limit(max_requests=10, window_seconds=60):
 """
 Sliding window rate limiter with sorted set.
 More accurate than fixed window.
 """
 def decorator(func):
 @wraps(func)
 def wrapper(user_id, *args, **kwargs):
 key = f'rate_limit_sw:{func.__name__}:{user_id}'
 now = time.time()
 window_start = now - window_seconds

 # Delete old entries
 r.zremrangebyscore(key, 0, window_start)

 # Current request count
 current_requests = r.zcard(key)

 if current_requests >= max_requests:
 raise RateLimitExceeded(
 f'Rate limit exceeded! Max {max_requests} requests/{window_seconds}s'
 )

 # Add new request
 r.zadd(key, {f'{now}': now})
 r.expire(key, window_seconds)

 return func(user_id, *args, **kwargs)

 return wrapper
 return decorator

# Example API functions
@rate_limit(max_requests=5, window_seconds=10)
def api_search(user_id, query):
 """API search - max 5 requests / 10 seconds"""
 return f'Search results: {query}'

@sliding_window_rate_limit(max_requests=3, window_seconds=5)
def api_upload(user_id, filename):
 """File upload - max 3 requests / 5 seconds"""
 return f'File uploaded: {filename}'

# Test
print("=== Rate Limiter Demo ===\n")

user_id = 'user123'

# Fixed window test
print("Fixed Window Rate Limiter:")
for i in range(7):
 try:
 result = api_search(user_id, f'query_{i}')
 print(f" Request #{i+1}: {result}")
 except RateLimitExceeded as e:
 print(f" Request #{i+1}: {e}")

print("\nSliding Window Rate Limiter:")
for i in range(5):
 try:
 result = api_upload(user_id, f'file_{i}.txt')
 print(f" Request #{i+1}: {result}")
 time.sleep(0.5)
 except RateLimitExceeded as e:
 print(f" Request #{i+1}: {e}")

# Retry after waiting
print("\nWaiting 6 seconds...")
time.sleep(6)

try:
 result = api_upload(user_id, 'new_file.txt')
 print(f" Retry: {result}")
except RateLimitExceeded as e:
 print(f" Retry: {e}")
```

**Task 7:** Create a function that returns how many requests a user has remaining in the current time window!

---

## 4. Kubernetes Integration

### 4.1 Redis Deployment in K8s (30 minutes)

**Exercise: Running Redis on Kubernetes**

Create a `k8s-redis.yaml` file:

```yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
 name: redis-config
data:
 redis.conf: |
 maxmemory 256mb
 maxmemory-policy allkeys-lru
 save ""
---
apiVersion: v1
kind: Service
metadata:
 name: redis
spec:
 type: ClusterIP
 ports:
 - port: 6379
 targetPort: 6379
 selector:
 app: redis
---
apiVersion: apps/v1
kind: Deployment
metadata:
 name: redis
spec:
 replicas: 1
 selector:
 matchLabels:
 app: redis
 template:
 metadata:
 labels:
 app: redis
 spec:
 containers:
 - name: redis
 image: redis:7-alpine
 ports:
 - containerPort: 6379
 resources:
 requests:
 cpu: 100m
 memory: 128Mi
 limits:
 cpu: 500m
 memory: 512Mi
 volumeMounts:
 - name: config
 mountPath: /usr/local/etc/redis/redis.conf
 subPath: redis.conf
 command:
 - redis-server
 - /usr/local/etc/redis/redis.conf
 volumes:
 - name: config
 configMap:
 name: redis-config
```

**Deployment:**
```bash
kubectl apply -f k8s-redis.yaml

# Verification
kubectl get pods -l app=redis
kubectl logs -l app=redis

# Test connection
kubectl port-forward svc/redis 6379:6379

# In another terminal:
redis-cli ping
```

### 4.2 Python App with Redis in K8s (30 minutes)

**Exercise: Web application with Redis backend**

Create an `app.py` file:

```python
from flask import Flask, jsonify
import redis
import os

app = Flask(__name__)

# Redis connection (Kubernetes service name)
REDIS_HOST = os.getenv('REDIS_HOST', 'redis')
REDIS_PORT = int(os.getenv('REDIS_PORT', 6379))

r = redis.Redis(host=REDIS_HOST, port=REDIS_PORT, decode_responses=True)

@app.route('/')
def index():
 return jsonify({'message': 'Redis Demo App'})

@app.route('/counter')
def counter():
 """Page view counter"""
 count = r.incr('page_views')
 return jsonify({'page_views': count})

@app.route('/health')
def health():
 """Health check endpoint"""
 try:
 r.ping()
 return jsonify({'status': 'healthy', 'redis': 'connected'})
 except:
 return jsonify({'status': 'unhealthy', 'redis': 'disconnected'}), 500

if __name__ == '__main__':
 app.run(host='0.0.0.0', port=8080)
```

**Dockerfile:**
```dockerfile
FROM python:3.11-slim

WORKDIR /app

RUN pip install flask redis

COPY app.py .

CMD ["python", "app.py"]
```

**Kubernetes deployment:**

Create a `k8s-app.yaml` file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: redis-demo-app
spec:
 replicas: 2
 selector:
 matchLabels:
 app: redis-demo-app
 template:
 metadata:
 labels:
 app: redis-demo-app
 spec:
 containers:
 - name: app
 image: your-registry/redis-demo-app:latest
 ports:
 - containerPort: 8080
 env:
 - name: REDIS_HOST
 value: "redis"
 - name: REDIS_PORT
 value: "6379"
 livenessProbe:
 httpGet:
 path: /health
 port: 8080
 initialDelaySeconds: 5
 periodSeconds: 10
---
apiVersion: v1
kind: Service
metadata:
 name: redis-demo-app
spec:
 type: ClusterIP
 ports:
 - port: 80
 targetPort: 8080
 selector:
 app: redis-demo-app
```

**Build and deploy:**
```bash
# Build Docker image (modify registry name!)
docker build -t your-registry/redis-demo-app:latest .
docker push your-registry/redis-demo-app:latest

# Deploy
kubectl apply -f k8s-app.yaml

# Test
kubectl port-forward svc/redis-demo-app 8080:80

# In another terminal:
curl http://localhost:8080/counter
curl http://localhost:8080/counter
curl http://localhost:8080/health
```

---

## 5. Final Project - Mini Application

### Project: Real-time Leaderboard API

**Goal:** Create a REST API that manages a game leaderboard in Redis.

**Requirements:**
1. **Record score** - POST /score
2. **Top 10 list** - GET /leaderboard
3. **Player statistics** - GET /player/{id}
4. **Rate limiting** - Max 10 requests/minute per player
5. **Cache** - Cache top 10 list for 30 seconds

**Starter template:**

```python
from flask import Flask, request, jsonify
import redis
import time

app = Flask(__name__)
r = redis.Redis(host='localhost', port=6379, decode_responses=True)

LEADERBOARD_KEY = 'game:leaderboard'
CACHE_KEY = 'cache:top10'
CACHE_TTL = 30

@app.route('/score', methods=['POST'])
def submit_score():
 """
 Submit score.
 Body: {"player_id": "player123", "score": 1500}
 """
 # TODO: Implement!
 # - Check rate limiting
 # - Add score to sorted set
 # - Invalidate cache
 pass

@app.route('/leaderboard', methods=['GET'])
def get_leaderboard():
 """
 Get top 10 list (cached).
 """
 # TODO: Implement!
 # - Check cache
 # - If no cache, query Redis
 # - Cache the result
 pass

@app.route('/player/<player_id>', methods=['GET'])
def get_player_stats(player_id):
 """
 Player statistics.
 Returns: score, rank, percentile
 """
 # TODO: Implement!
 pass

if __name__ == '__main__':
 app.run(debug=True, port=5000)
```

**Task 8 (Big task):** Complete the above application using all your learned Redis knowledge!

**Bonus challenges:**
- Daily/weekly/monthly leaderboards in separate sorted sets
- Store historical data (record every score)
- WebSocket push notifications on leaderboard changes
- Export Prometheus metrics

---

## Further Learning

### Advanced Topics

1. **Redis Cluster** - Horizontal scaling
2. **Redis Sentinel** - High availability
3. **Pub/Sub** - Real-time messaging
4. **Redis Streams** - Event streaming (Kafka-like)
5. **Lua Scripting** - Atomic operations in Redis
6. **Redis Modules** - RediSearch, RedisJSON, RedisGraph

### Recommended Resources

**Documentation:**
- https://redis.io/docs/
- https://redis.io/commands/

**Interactive learning:**
- https://try.redis.io/ - Try Redis in browser
- https://university.redis.com/ - Free courses

**Example projects:**
- Celery (Python task queue)
- Sidekiq (Ruby background jobs)
- Bull (Node.js job queue)

**Books:**
- "Redis in Action" - Josiah Carlson
- "Mastering Redis" - Jeremy Nelson

---

## Checklist

**Basics:**
- [ ] Redis installation and connection
- [ ] SET/GET/DEL/EXPIRE commands
- [ ] Python redis client usage
- [ ] TTL and expiration handling

**Data structures:**
- [ ] Lists (queue, recent items)
- [ ] Hashes (objects)
- [ ] Sets (unique items, operations)
- [ ] Sorted Sets (leaderboard, time-series data)

**Real-world usage:**
- [ ] Cache implementation
- [ ] Session store
- [ ] Rate limiting
- [ ] Counter/statistics

**Kubernetes:**
- [ ] Redis deployment in K8s
- [ ] Application connecting to Redis service
- [ ] Health check and monitoring

**Project:**
- [ ] Final project completed
- [ ] Rate limiting implemented
- [ ] Cache usage
- [ ] REST API working

---

**Next step:** Start with Chapter 1 and proceed at your own pace! Try the exercises at the end of each chapter, and only move forward when you understand the material. Good luck! 
