# System Design Fundamentals

## What is System Design?

System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements.

## Why System Design Interviews?

- Test ability to design scalable systems
- Evaluate trade-off analysis skills
- Assess communication and problem-solving
- Gauge experience with real-world systems

## System Design Interview Framework

### 1. Understand Requirements (5-10 mins)
- **Functional Requirements**: What should the system do?
- **Non-Functional Requirements**: Scale, performance, availability
- **Constraints**: Time, resources, technical limitations

#### Questions to Ask:
- Who will use the system?
- How many users? (scale)
- What's the expected traffic pattern?
- What's the data size?
- What's more important: consistency or availability?
- What are the latency requirements?

### 2. Back-of-the-Envelope Estimation (5 mins)
- Calculate traffic estimates
- Storage estimates
- Bandwidth estimates
- Memory estimates

Example:
```
Users: 100M daily active users
Requests per day: 100M * 10 = 1B
Requests per second: 1B / 86400 ≈ 12K RPS
Peak traffic (3x): 36K RPS
Storage per day: 1B * 1KB = 1TB/day
Storage per year: 365TB ≈ 0.365PB
```

### 3. System Interface Definition (5 mins)
Define API endpoints.

```
POST /api/v1/posts
GET /api/v1/posts/{postId}
DELETE /api/v1/posts/{postId}
GET /api/v1/users/{userId}/feed
```

### 4. Define Data Model (5 mins)
Design database schema.

```sql
User {
    user_id: UUID
    username: VARCHAR
    email: VARCHAR
    created_at: TIMESTAMP
}

Post {
    post_id: UUID
    user_id: UUID
    content: TEXT
    created_at: TIMESTAMP
}
```

### 5. High-Level Design (10-15 mins)
Draw system architecture diagram.

Components:
- Clients (web, mobile)
- Load balancers
- Application servers
- Databases
- Caches
- Message queues
- CDN

### 6. Detailed Design (15-20 mins)
Deep dive into specific components.

Topics to cover:
- Database sharding strategy
- Caching strategy
- Load balancing approach
- API design
- Data flow
- Replication strategy

### 7. Identify Bottlenecks (5-10 mins)
- Single points of failure
- Performance bottlenecks
- Scalability issues
- Monitoring and alerting

## Key Concepts

### CAP Theorem
A distributed system can provide only 2 of 3 guarantees:
- **Consistency**: All nodes see same data
- **Availability**: System always responds
- **Partition Tolerance**: System works despite network failures

### ACID Properties
Database transaction properties:
- **Atomicity**: All or nothing
- **Consistency**: Valid state transitions
- **Isolation**: Concurrent transactions don't interfere
- **Durability**: Committed data persists

### BASE Properties
Alternative to ACID for distributed systems:
- **Basically Available**: System appears available
- **Soft state**: State may change without input
- **Eventually consistent**: System becomes consistent over time

### Consistency Patterns

#### Strong Consistency
All reads receive most recent write.
- Use: Financial systems, inventory
- Trade-off: Higher latency

#### Eventual Consistency
Reads may not reflect latest write immediately.
- Use: Social media feeds, DNS
- Trade-off: Lower latency, complex conflict resolution

#### Weak Consistency
No guarantee reads will reflect recent writes.
- Use: Real-time streaming, gaming
- Trade-off: Best performance

### Availability Patterns

#### Fail-over
- **Active-Passive**: Passive server takes over if active fails
- **Active-Active**: Both servers handle traffic

#### Replication
- **Master-Slave**: Master handles writes, slaves handle reads
- **Master-Master**: Both handle writes and reads

## Common System Design Patterns

### 1. Load Balancing
Distribute traffic across multiple servers.

Types:
- Layer 4 (Transport): Based on IP/port
- Layer 7 (Application): Based on content

Algorithms:
- Round Robin
- Least Connections
- Least Response Time
- IP Hash

### 2. Caching
Store frequently accessed data in memory.

Levels:
- Client-side
- CDN
- Web server
- Database
- Application

Strategies:
- Cache-aside
- Write-through
- Write-behind
- Refresh-ahead

### 3. Database Sharding
Partition data across multiple databases.

Strategies:
- Hash-based
- Range-based
- Geography-based
- Directory-based

### 4. Asynchronous Processing
Use message queues for time-consuming tasks.

Benefits:
- Improved response time
- Better fault tolerance
- Easier scaling

### 5. Microservices
Break monolith into small, independent services.

Benefits:
- Independent deployment
- Technology diversity
- Better scalability

Challenges:
- Increased complexity
- Network overhead
- Data consistency

## Performance Metrics

### Latency
Time to perform action.
- **P50**: 50th percentile
- **P95**: 95th percentile
- **P99**: 99th percentile

### Throughput
Number of operations per time unit.
- Requests per second (RPS)
- Queries per second (QPS)

### Availability
```
Availability = Uptime / (Uptime + Downtime)
99.9% = 43.8 minutes downtime/month
99.99% = 4.38 minutes downtime/month
99.999% = 26.3 seconds downtime/month
```

## Numbers Every Engineer Should Know

```
L1 cache reference: 0.5 ns
L2 cache reference: 7 ns
Main memory reference: 100 ns
SSD random read: 150,000 ns (150 μs)
Network within datacenter: 500,000 ns (0.5 ms)
Disk seek: 10,000,000 ns (10 ms)
Network between continents: 150,000,000 ns (150 ms)
```

## Common Mistakes to Avoid

1. Not asking clarifying questions
2. Jumping into details too quickly
3. Not considering scale
4. Ignoring trade-offs
5. Not thinking about failures
6. Over-engineering
7. Not managing time well
8. Poor communication

## Tips for Success

1. **Think out loud**: Explain your thought process
2. **Start simple**: Begin with basic design, then iterate
3. **Use diagrams**: Visualize your architecture
4. **Consider trade-offs**: Discuss pros and cons
5. **Ask questions**: Clarify requirements
6. **Be flexible**: Adapt based on feedback
7. **Practice**: Do mock interviews
8. **Know the fundamentals**: Caching, databases, networks

## Resources

- "Designing Data-Intensive Applications" by Martin Kleppmann
- "System Design Interview" by Alex Xu
- High Scalability blog
- AWS/GCP/Azure architecture documentation
