# Scalability Principles

## What is Scalability?

Scalability is the ability of a system to handle increased load by adding resources.

## Types of Scaling

### 1. Vertical Scaling (Scale Up)
Add more power to existing machine (CPU, RAM, storage).

**Pros:**
- Simple to implement
- No code changes needed
- Lower complexity

**Cons:**
- Hardware limits
- Single point of failure
- Expensive at scale
- Downtime during upgrade

**Use when:**
- Starting out
- Simple applications
- Budget constraints

### 2. Horizontal Scaling (Scale Out)
Add more machines to the pool.

**Pros:**
- No theoretical limit
- Better fault tolerance
- More cost-effective
- Can be automated

**Cons:**
- Complex architecture
- Requires load balancing
- Data consistency challenges
- Network overhead

**Use when:**
- High growth expected
- Need fault tolerance
- Long-term scalability

## Scalability Patterns

### 1. Stateless Services
Design services without session state.

```python
# Bad: Stateful
class UserService:
    def __init__(self):
        self.sessions = {}
    
    def login(self, user_id):
        self.sessions[user_id] = create_session()

# Good: Stateless
class UserService:
    def login(self, user_id):
        token = create_session()
        cache.set(token, user_id)
        return token
```

**Benefits:**
- Easy to scale horizontally
- No session affinity needed
- Simplifies load balancing

### 2. Database Scaling

#### Read Replicas
Create read-only copies of database.

```
Master (Write) → Slave1 (Read)
               → Slave2 (Read)
               → Slave3 (Read)
```

**Benefits:**
- Distribute read load
- Improve read performance
- Backup for disaster recovery

**Challenges:**
- Replication lag
- Write bottleneck still exists
- Consistency issues

#### Database Sharding
Partition data across multiple databases.

**Horizontal Sharding (Rows):**
```
User IDs 1-1000   → Shard 1
User IDs 1001-2000 → Shard 2
User IDs 2001-3000 → Shard 3
```

**Vertical Sharding (Columns):**
```
User basic info → Shard 1
User profile   → Shard 2
User activity  → Shard 3
```

**Sharding Strategies:**

1. **Hash-based:**
```python
shard_id = hash(user_id) % num_shards
```
- Pros: Even distribution
- Cons: Hard to add shards

2. **Range-based:**
```python
if user_id < 1000: shard = 0
elif user_id < 2000: shard = 1
else: shard = 2
```
- Pros: Easy range queries
- Cons: Uneven distribution

3. **Geographic:**
```python
shard = user.region  # US, EU, ASIA
```
- Pros: Lower latency
- Cons: Uneven load

4. **Directory-based:**
```python
shard = lookup_table[user_id]
```
- Pros: Flexible
- Cons: Lookup overhead

### 3. Caching Strategy

#### Cache Levels
```
Client → CDN → Web Server Cache → Application Cache → Database Cache
```

#### Caching Patterns

**1. Cache-Aside (Lazy Loading)**
```python
def get_user(user_id):
    # Try cache first
    user = cache.get(f"user:{user_id}")
    if user:
        return user
    
    # Cache miss: load from DB
    user = db.query(f"SELECT * FROM users WHERE id={user_id}")
    cache.set(f"user:{user_id}", user, ttl=3600)
    return user
```

**2. Write-Through**
```python
def update_user(user_id, data):
    # Write to cache and DB synchronously
    db.update(user_id, data)
    cache.set(f"user:{user_id}", data)
```

**3. Write-Behind (Write-Back)**
```python
def update_user(user_id, data):
    # Write to cache immediately
    cache.set(f"user:{user_id}", data)
    # Async write to DB
    queue.publish("user_update", {"id": user_id, "data": data})
```

**4. Refresh-Ahead**
```python
def get_user(user_id):
    user = cache.get(f"user:{user_id}")
    if cache.ttl(f"user:{user_id}") < threshold:
        # Proactively refresh
        asyncio.create_task(refresh_cache(user_id))
    return user
```

### 4. Load Balancing

#### Algorithms

**1. Round Robin**
```
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1 (repeat)
```

**2. Least Connections**
Route to server with fewest active connections.

**3. Least Response Time**
Route to server with lowest latency.

**4. IP Hash**
```python
server = servers[hash(client_ip) % len(servers)]
```

**5. Weighted Round Robin**
```
Server 1 (weight=3) gets 3 requests
Server 2 (weight=1) gets 1 request
```

### 5. Asynchronous Processing

#### Message Queue Pattern
```
Client → API Server → Message Queue → Workers → Database
```

**Benefits:**
- Decouple components
- Handle traffic spikes
- Retry failed operations
- Prioritize tasks

**Use cases:**
- Email sending
- Image processing
- Report generation
- Log processing

#### Example with Queue
```python
# Producer
def create_order(order_data):
    order_id = db.insert(order_data)
    queue.publish("process_order", {"order_id": order_id})
    return {"order_id": order_id, "status": "pending"}

# Consumer
def process_order_worker():
    while True:
        message = queue.consume("process_order")
        order = db.get(message["order_id"])
        
        # Time-consuming tasks
        send_confirmation_email(order)
        update_inventory(order)
        notify_shipping(order)
        
        db.update(order.id, status="processed")
```

### 6. Content Delivery Network (CDN)

Cache static content at edge locations closer to users.

**What to cache:**
- Images, videos
- CSS, JavaScript
- Static HTML pages
- API responses (with appropriate headers)

**Benefits:**
- Reduced latency
- Lower bandwidth costs
- Better availability
- DDoS protection

### 7. Microservices Architecture

Break monolith into independent services.

```
Monolith:
[User + Order + Payment + Shipping]

Microservices:
[User Service] [Order Service] [Payment Service] [Shipping Service]
```

**Benefits:**
- Independent scaling
- Technology flexibility
- Team autonomy
- Fault isolation

**Challenges:**
- Distributed system complexity
- Network latency
- Data consistency
- Service discovery
- Testing complexity

## Scalability Checklist

### Application Layer
- [ ] Stateless services
- [ ] Horizontal scaling capability
- [ ] Connection pooling
- [ ] Async processing for long tasks
- [ ] API rate limiting

### Database Layer
- [ ] Read replicas for read-heavy workloads
- [ ] Sharding strategy for writes
- [ ] Proper indexing
- [ ] Query optimization
- [ ] Connection pooling

### Caching Layer
- [ ] Multiple cache levels
- [ ] Appropriate TTL
- [ ] Cache invalidation strategy
- [ ] Cache warming
- [ ] Fallback handling

### Infrastructure
- [ ] Load balancers
- [ ] Auto-scaling policies
- [ ] Health checks
- [ ] Monitoring and alerts
- [ ] CDN for static content

### Data Flow
- [ ] Message queues for async tasks
- [ ] Circuit breakers for failures
- [ ] Retry mechanisms
- [ ] Idempotent operations
- [ ] Event streaming for real-time data

## Performance Optimization

### 1. Database Optimization
- Index frequently queried columns
- Denormalize for read performance
- Archive old data
- Use appropriate data types
- Optimize queries (EXPLAIN)

### 2. Application Optimization
- Reduce round trips
- Batch operations
- Connection pooling
- Compress data
- Use pagination

### 3. Network Optimization
- Use CDN
- Enable compression (gzip)
- HTTP/2 or HTTP/3
- Reduce payload size
- Connection keep-alive

## Monitoring and Metrics

### Key Metrics
- **Response time**: P50, P95, P99
- **Throughput**: Requests per second
- **Error rate**: 4xx, 5xx errors
- **Saturation**: CPU, memory, disk usage
- **Traffic patterns**: Peak vs average

### Monitoring Tools
- Application Performance Monitoring (APM)
- Log aggregation
- Metrics collection
- Distributed tracing
- Alerting systems

## Common Bottlenecks

1. **Database**: Too many queries, slow queries
2. **Network**: High latency, bandwidth limits
3. **CPU**: Complex computations
4. **Memory**: Large object graphs, memory leaks
5. **Disk I/O**: Slow reads/writes

## Tips

- Start simple, optimize when needed
- Measure before optimizing
- Know your bottlenecks
- Plan for 10x growth
- Design for failure
- Monitor everything
- Load test regularly
- Document scaling decisions
