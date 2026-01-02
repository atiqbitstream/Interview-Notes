# Common System Design Questions

## 1. Design URL Shortener (like TinyURL, bit.ly)

### Requirements
**Functional:**
- Shorten long URLs
- Redirect to original URL
- Custom short URLs (optional)
- URL expiration (optional)
- Analytics (optional)

**Non-Functional:**
- High availability
- Low latency for redirects
- Scalable (billions of URLs)

### Capacity Estimation
```
Write: 500M new URLs per month
      ≈ 200 URLs per second

Read: 100:1 read-to-write ratio
     = 20K redirects per second

Storage: 500M * 12 months * 5 years = 30B URLs
        Each URL ≈ 500 bytes
        Total = 15TB
```

### API Design
```
POST /api/v1/shorten
  Body: { "long_url": "https://example.com/very/long/url" }
  Response: { "short_url": "https://short.ly/abc123" }

GET /{shortCode}
  Response: 302 Redirect to long_url
```

### Design

**Database Schema:**
```sql
CREATE TABLE urls (
    id BIGINT PRIMARY KEY,
    short_code VARCHAR(10) UNIQUE,
    long_url TEXT NOT NULL,
    created_at TIMESTAMP,
    expires_at TIMESTAMP,
    user_id BIGINT,
    INDEX (short_code)
);
```

**Short Code Generation:**
```python
# Base62 encoding
def encode_base62(num):
    chars = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'
    base = len(chars)
    result = []
    while num > 0:
        result.append(chars[num % base])
        num //= base
    return ''.join(reversed(result))

# Use auto-increment ID
short_code = encode_base62(db_id)
```

**Components:**
- Load Balancer
- Application Servers
- Cache (Redis) for hot URLs
- Database (sharded)
- Analytics Service

**Flow:**
1. User submits long URL
2. Generate unique short code
3. Store mapping in DB
4. Return short URL
5. Redirect: Check cache → DB → Redirect

### Deep Dive Topics
- **Caching**: Cache popular URLs
- **Database**: Sharding by short_code hash
- **Analytics**: Async writes to analytics DB
- **Custom URLs**: Check uniqueness, reserve keywords

---

## 2. Design Instagram

### Requirements
**Functional:**
- Upload/view photos
- Follow users
- News feed
- Like/comment on photos

**Non-Functional:**
- High availability
- Low latency for feed
- Eventually consistent
- Scale: 500M users, 100M DAU

### Capacity Estimation
```
Users: 500M total, 100M DAU
Photos uploaded: 2M per day
Average photo size: 2MB
Storage per day: 2M * 2MB = 4TB per day
Storage per year: 1.46PB
```

### Database Schema
```sql
Users:
  user_id, username, email, bio, profile_pic

Photos:
  photo_id, user_id, image_url, caption, created_at

Follows:
  follower_id, followee_id, created_at

Likes:
  user_id, photo_id, created_at

Comments:
  comment_id, photo_id, user_id, text, created_at
```

### Design

**Components:**
- CDN for photo storage
- Object storage (S3)
- Database (SQL for metadata, NoSQL for feed)
- Cache (Redis)
- Message queue for async tasks

**News Feed Generation:**

*Pull Model (on read):*
```python
def get_feed(user_id):
    following = db.get_following(user_id)
    posts = []
    for followee in following:
        posts.extend(db.get_recent_posts(followee, limit=100))
    return sorted(posts, key=lambda x: x.created_at, reverse=True)[:20]
```

*Push Model (on write):*
```python
def create_post(user_id, photo):
    post_id = db.insert_post(user_id, photo)
    followers = db.get_followers(user_id)
    for follower in followers:
        cache.lpush(f"feed:{follower}", post_id)
```

*Hybrid:*
- Push for users with few followers
- Pull for celebrities

### Deep Dive
- **Photo Upload**: Upload to S3, generate thumbnails async
- **Feed Ranking**: ML-based ranking for engagement
- **Sharding**: Shard by user_id
- **Replication**: Master-slave for reads

---

## 3. Design Twitter

### Requirements
**Functional:**
- Post tweets
- Follow users
- Timeline (home & user)
- Search tweets

**Non-Functional:**
- High availability
- Low latency for timeline
- 400M users, 200M DAU
- 400M tweets per day

### Database Schema
```sql
Users:
  user_id, username, email, created_at

Tweets:
  tweet_id, user_id, text, created_at

Follows:
  follower_id, followee_id, created_at
```

### Timeline Generation

**Home Timeline (Fan-out on write):**
```python
def post_tweet(user_id, text):
    tweet_id = db.insert_tweet(user_id, text)
    followers = db.get_followers(user_id)
    
    for follower in followers:
        cache.lpush(f"timeline:{follower}", tweet_id)
        cache.ltrim(f"timeline:{follower}", 0, 999)  # Keep 1000 tweets
```

**User Timeline (simple query):**
```python
def get_user_timeline(user_id):
    return db.query("SELECT * FROM tweets WHERE user_id = ? ORDER BY created_at DESC LIMIT 20", user_id)
```

### Celebrities Problem
Don't fan-out for users with millions of followers.
```python
def get_home_timeline(user_id):
    # Get from cache (fanout)
    timeline = cache.lrange(f"timeline:{user_id}", 0, 19)
    
    # Add celebrity tweets (pull)
    celebrities = get_celebrities_following(user_id)
    celeb_tweets = db.get_recent_tweets(celebrities)
    
    # Merge and sort
    return merge_sort(timeline, celeb_tweets)
```

---

## 4. Design YouTube/Netflix

### Requirements
**Functional:**
- Upload videos
- Stream videos
- Search videos
- Recommendations

**Non-Functional:**
- Low latency streaming
- High availability
- Billions of videos
- High bandwidth

### Components
- **Upload Service**: Process and transcode videos
- **CDN**: Serve videos from edge locations
- **Encoding Service**: Multiple resolutions (360p, 720p, 1080p, 4K)
- **Metadata Service**: Store video info
- **Recommendation Service**: ML-based suggestions
- **Search Service**: Elasticsearch

### Video Upload Flow
```
1. User uploads video → Upload Service
2. Store raw video in S3
3. Queue encoding job
4. Encoding Service:
   - Transcode to multiple resolutions
   - Generate thumbnails
   - Store in CDN
5. Update metadata in DB
6. Notify user
```

### Video Streaming
```
Client → CDN (edge location) → Origin Servers
```

Use adaptive bitrate streaming (HLS, DASH):
```
video_360p.m3u8
video_720p.m3u8
video_1080p.m3u8
```

### Deep Dive
- **Content Delivery**: Multi-region CDN
- **DRM**: Encrypt premium content
- **Live Streaming**: Different architecture, lower latency
- **Recommendations**: Collaborative filtering, content-based

---

## 5. Design Uber/Lyft

### Requirements
**Functional:**
- Rider requests ride
- Driver accepts ride
- Real-time location tracking
- ETA calculation
- Payment processing

**Non-Functional:**
- Low latency for matching
- High availability
- Real-time updates
- Scalable (millions of rides per day)

### Database Schema
```sql
Users:
  user_id, name, email, phone, type (rider/driver)

Rides:
  ride_id, rider_id, driver_id, status, pickup_location, dropoff_location, created_at

Locations:
  driver_id, latitude, longitude, updated_at
```

### Design

**Location Tracking:**
```python
# Driver app sends location every 5 seconds
def update_location(driver_id, lat, lon):
    cache.geoadd("drivers", lon, lat, driver_id)
    cache.expire(f"driver:{driver_id}", 300)  # 5 min TTL
```

**Driver Matching:**
```python
def find_nearby_drivers(lat, lon, radius_km=5):
    # Use geospatial index
    return cache.georadius("drivers", lon, lat, radius_km, unit='km')

def request_ride(rider_id, pickup_lat, pickup_lon):
    nearby_drivers = find_nearby_drivers(pickup_lat, pickup_lon)
    for driver in sorted(nearby_drivers, key=lambda d: d.distance):
        if notify_driver(driver, ride_request):
            return driver
```

**Components:**
- Location Service (Redis Geospatial)
- Matching Service
- Routing Service (Google Maps API)
- Payment Service
- Notification Service (WebSocket/Push)

### Deep Dive
- **Real-time updates**: WebSocket connections
- **ETA calculation**: Google Directions API
- **Surge pricing**: Dynamic pricing based on demand
- **Database**: Shard by geo-location

---

## Interview Tips

### Framework
1. **Clarify requirements** (5 min)
2. **Capacity estimation** (5 min)
3. **API design** (5 min)
4. **Database schema** (5 min)
5. **High-level design** (15 min)
6. **Deep dive** (15 min)
7. **Bottlenecks** (5 min)

### Communication
- Think out loud
- Ask clarifying questions
- Discuss trade-offs
- Be flexible to feedback
- Draw diagrams

### Common Patterns
- Caching for read-heavy
- Message queues for async
- CDN for static content
- Sharding for scalability
- Replication for availability
- Load balancing for distribution
