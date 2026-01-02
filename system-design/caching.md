# Caching Strategies

## What is Caching?

Caching is storing copies of data in a faster storage layer to reduce latency and database load.

## Benefits of Caching

- **Reduced Latency**: Fast access to frequently used data
- **Lower Database Load**: Fewer queries to primary database
- **Cost Savings**: Reduced compute and database costs
- **Better Scalability**: Handle more traffic with same resources
- **Improved UX**: Faster response times

## Cache Levels

### 1. Client-Side Caching
Browser cache, mobile app cache.

```html
<!-- HTTP Cache Headers -->
Cache-Control: max-age=3600
ETag: "abc123"
Last-Modified: Wed, 21 Oct 2024 07:28:00 GMT
```

### 2. CDN (Content Delivery Network)
Cache static content at edge locations.

**What to cache:**
- Images, videos
- CSS, JavaScript
- Static HTML
- API responses (with care)

### 3. Load Balancer Cache
Some load balancers can cache responses.

### 4. Application Cache
In-memory cache within application servers.

```python
# Local cache
from functools import lru_cache

@lru_cache(maxsize=1000)
def get_user(user_id):
    return db.query(f"SELECT * FROM users WHERE id={user_id}")
```

### 5. Distributed Cache
Shared cache across application servers.

Examples: Redis, Memcached

```python
import redis

cache = redis.Redis(host='localhost', port=6379)

def get_user(user_id):
    # Try cache first
    cached = cache.get(f"user:{user_id}")
    if cached:
        return json.loads(cached)
    
    # Cache miss
    user = db.query(f"SELECT * FROM users WHERE id={user_id}")
    cache.setex(f"user:{user_id}", 3600, json.dumps(user))
    return user
```

### 6. Database Cache
Query cache, buffer pool.

## Caching Patterns

### 1. Cache-Aside (Lazy Loading)

**Flow:**
1. Check cache
2. If miss, load from DB
3. Update cache
4. Return data

```python
def get_user(user_id):
    # Read from cache
    user = cache.get(f"user:{user_id}")
    if user:
        return user
    
    # Cache miss: read from DB
    user = db.query(f"SELECT * FROM users WHERE id={user_id}")
    
    # Write to cache
    cache.set(f"user:{user_id}", user, ttl=3600)
    return user

def update_user(user_id, data):
    # Update DB
    db.update(user_id, data)
    
    # Invalidate cache
    cache.delete(f"user:{user_id}")
```

**Pros:**
- Only cache requested data
- Resilient to cache failures
- Cache can be regenerated

**Cons:**
- Cache miss penalty (3 round trips)
- Stale data possible
- Cache warm-up needed

**Use when:**
- Read-heavy workloads
- Cache failures tolerable
- Data updated infrequently

### 2. Read-Through Cache

Cache sits between app and database.

```python
# Cache handles DB interaction
def get_user(user_id):
    return cache.get(f"user:{user_id}")
    # Cache automatically loads from DB if miss
```

**Pros:**
- Simpler application code
- Automatic cache population

**Cons:**
- First request always slow
- Cache must handle DB logic

### 3. Write-Through Cache

Write to cache and DB synchronously.

```python
def update_user(user_id, data):
    # Write to DB
    db.update(user_id, data)
    
    # Write to cache
    cache.set(f"user:{user_id}", data, ttl=3600)
    
    return data
```

**Pros:**
- Cache always consistent
- No stale data
- Simple to understand

**Cons:**
- Higher write latency
- Unnecessary cache writes
- Cache could fill with rarely read data

**Use when:**
- Read after write common
- Consistency critical
- Write performance acceptable

### 4. Write-Behind (Write-Back) Cache

Write to cache immediately, DB asynchronously.

```python
def update_user(user_id, data):
    # Write to cache immediately
    cache.set(f"user:{user_id}", data)
    
    # Queue DB write
    queue.publish("user_update", {
        "user_id": user_id,
        "data": data
    })
    
    return data

# Background worker
def process_user_updates():
    while True:
        msg = queue.consume("user_update")
        db.update(msg["user_id"], msg["data"])
```

**Pros:**
- Fast writes
- Batch DB operations
- Better write throughput

**Cons:**
- Risk of data loss
- Complexity
- Eventual consistency

**Use when:**
- High write volume
- Write performance critical
- Can tolerate data loss risk

### 5. Refresh-Ahead

Proactively refresh before expiry.

```python
def get_user(user_id):
    user = cache.get(f"user:{user_id}")
    ttl = cache.ttl(f"user:{user_id}")
    
    # Refresh if TTL below threshold
    if ttl < 300:  # 5 minutes
        asyncio.create_task(refresh_user_cache(user_id))
    
    return user

async def refresh_user_cache(user_id):
    user = db.query(f"SELECT * FROM users WHERE id={user_id}")
    cache.set(f"user:{user_id}", user, ttl=3600)
```

**Pros:**
- Reduced latency
- Fewer cache misses
- Better user experience

**Cons:**
- Increased complexity
- Predicting what to refresh
- Wasted refreshes

**Use when:**
- Predictable access patterns
- Hot data known
- Latency sensitive

## Cache Eviction Policies

### 1. LRU (Least Recently Used)
Remove least recently accessed items.

**Use when:**
- Recent data more important
- Access patterns temporal

### 2. LFU (Least Frequently Used)
Remove least frequently accessed items.

**Use when:**
- Popular data matters
- Access frequency predictive

### 3. FIFO (First In First Out)
Remove oldest items first.

**Use when:**
- Simpler logic needed
- All data equally important

### 4. TTL (Time To Live)
Remove after fixed time.

```python
cache.setex("user:123", 3600, user_data)  # 1 hour
```

**Use when:**
- Data has clear freshness requirement
- Predictable data lifecycle

### 5. Random
Remove random items.

**Use when:**
- Simplest implementation
- No clear access pattern

## Cache Invalidation

> "There are only two hard things in Computer Science: cache invalidation and naming things." - Phil Karlton

### 1. Time-Based (TTL)
```python
cache.setex("user:123", 3600, user_data)
```

**Pros:**
- Simple
- Automatic

**Cons:**
- Stale data before expiry
- Unnecessary refreshes

### 2. Event-Based
Invalidate on data change.

```python
def update_user(user_id, data):
    db.update(user_id, data)
    cache.delete(f"user:{user_id}")
    
    # Also invalidate related caches
    cache.delete(f"user_posts:{user_id}")
    cache.delete(f"user_followers:{user_id}")
```

### 3. Tagging
Tag related cache entries.

```python
# Set with tags
cache.set("user:123", user_data, tags=["user", "profile"])
cache.set("user:123:posts", posts, tags=["user", "posts"])

# Invalidate by tag
cache.invalidate_tag("user")  # Removes all user-tagged caches
```

### 4. Version-Based
Include version in cache key.

```python
def get_user(user_id, version):
    return cache.get(f"user:{user_id}:v{version}")

# On update, increment version
def update_user(user_id, data):
    db.update(user_id, data)
    version = increment_version(user_id)
    cache.set(f"user:{user_id}:v{version}", data)
```

## Cache Consistency Patterns

### 1. Strong Consistency
Cache always reflects DB state.

**Implementation:**
- Write-through caching
- Synchronous invalidation
- Two-phase commit

**Trade-off:** Higher latency

### 2. Eventual Consistency
Cache eventually reflects DB state.

**Implementation:**
- Write-behind caching
- Async invalidation
- TTL-based expiry

**Trade-off:** Temporary stale data

## Common Caching Problems

### 1. Cache Stampede (Thundering Herd)

Many requests try to regenerate same cache entry.

```python
# Problem
def get_popular_item():
    item = cache.get("popular_item")
    if not item:
        # Many requests execute this simultaneously
        item = expensive_db_query()
        cache.set("popular_item", item)
    return item

# Solution: Use lock
import threading

lock = threading.Lock()

def get_popular_item():
    item = cache.get("popular_item")
    if not item:
        with lock:
            # Double-check after acquiring lock
            item = cache.get("popular_item")
            if not item:
                item = expensive_db_query()
                cache.set("popular_item", item, ttl=3600)
    return item
```

### 2. Cache Penetration

Requests for non-existent data bypass cache.

```python
# Solution: Cache null values
def get_user(user_id):
    user = cache.get(f"user:{user_id}")
    if user == "NULL":
        return None
    if not user:
        user = db.query(f"SELECT * FROM users WHERE id={user_id}")
        if user:
            cache.set(f"user:{user_id}", user, ttl=3600)
        else:
            cache.set(f"user:{user_id}", "NULL", ttl=60)
    return user
```

### 3. Cache Avalanche

Many cache entries expire simultaneously.

```python
# Solution: Add random jitter to TTL
import random

def set_with_jitter(key, value, base_ttl):
    jitter = random.randint(0, base_ttl // 10)
    cache.set(key, value, ttl=base_ttl + jitter)
```

### 4. Hot Key Problem

One key receives massive traffic.

**Solutions:**
- Client-side caching
- Replicate hot keys
- Use local cache
- Add request coalescing

## Redis Patterns

### 1. Rate Limiting
```python
def is_rate_limited(user_id, limit=100, window=60):
    key = f"rate_limit:{user_id}:{int(time.time() // window)}"
    count = cache.incr(key)
    if count == 1:
        cache.expire(key, window)
    return count > limit
```

### 2. Distributed Lock
```python
def acquire_lock(resource, timeout=10):
    lock_key = f"lock:{resource}"
    identifier = str(uuid.uuid4())
    
    if cache.set(lock_key, identifier, ex=timeout, nx=True):
        return identifier
    return None

def release_lock(resource, identifier):
    lock_key = f"lock:{resource}"
    if cache.get(lock_key) == identifier:
        cache.delete(lock_key)
        return True
    return False
```

### 3. Leaderboard
```python
# Add score
cache.zadd("leaderboard", {"user1": 100, "user2": 200})

# Get top 10
top_10 = cache.zrevrange("leaderboard", 0, 9, withscores=True)

# Get user rank
rank = cache.zrevrank("leaderboard", "user1")
```

## Best Practices

1. **Set appropriate TTL**: Balance freshness vs hit rate
2. **Monitor cache hit rate**: Aim for >80%
3. **Size cache appropriately**: Fit working set in memory
4. **Use compression**: For large values
5. **Handle cache failures gracefully**: Fall back to DB
6. **Warm up cache**: Preload popular data
7. **Use consistent hashing**: For distributed caches
8. **Monitor memory usage**: Prevent OOM
9. **Log cache misses**: Identify patterns
10. **Test cache invalidation**: Ensure consistency

## Metrics to Monitor

- Hit rate
- Miss rate
- Eviction rate
- Latency (P50, P95, P99)
- Memory usage
- Connection pool usage
- Cache size

## Common Interview Questions

- When would you use caching?
- Explain different caching strategies
- How do you handle cache invalidation?
- What's cache stampede and how to prevent it?
- How do you size a cache?
- Write-through vs write-behind?
- How do you ensure cache consistency?
