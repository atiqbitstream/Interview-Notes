# Load Balancing

## What is Load Balancing?

Load balancing distributes incoming network traffic across multiple servers to ensure no single server becomes overwhelmed.

## Benefits

- **High Availability**: Redundancy and failover
- **Scalability**: Handle more traffic by adding servers
- **Performance**: Distribute load for optimal response times
- **Flexibility**: Maintenance without downtime
- **Health Checking**: Route traffic only to healthy servers

## Load Balancing Layers

### Layer 4 (Transport Layer)
Routes based on IP and TCP/UDP port.

**Pros:**
- Fast (no content inspection)
- Simple
- Protocol agnostic

**Cons:**
- Limited routing decisions
- No content-aware routing
- Can't modify requests

### Layer 7 (Application Layer)
Routes based on HTTP headers, URL path, cookies.

**Pros:**
- Content-aware routing
- SSL termination
- Request modification
- Advanced features (rate limiting, caching)

**Cons:**
- Slower (content inspection)
- More complex
- Protocol specific

## Load Balancing Algorithms

### 1. Round Robin
Distribute requests sequentially.

```python
class RoundRobin:
    def __init__(self, servers):
        self.servers = servers
        self.index = 0
    
    def get_server(self):
        server = self.servers[self.index]
        self.index = (self.index + 1) % len(self.servers)
        return server
```

**Pros:**
- Simple
- Fair distribution

**Cons:**
- Ignores server load
- Ignores server capacity

**Use when:**
- Servers have similar capacity
- Requests have similar cost

### 2. Weighted Round Robin
Assign weights based on capacity.

```python
class WeightedRoundRobin:
    def __init__(self, servers):
        # servers = [("server1", 3), ("server2", 1)]
        self.servers = []
        for server, weight in servers:
            self.servers.extend([server] * weight)
        self.index = 0
    
    def get_server(self):
        server = self.servers[self.index]
        self.index = (self.index + 1) % len(self.servers)
        return server
```

**Use when:**
- Servers have different capacities
- Some servers more powerful

### 3. Least Connections
Route to server with fewest active connections.

```python
class LeastConnections:
    def __init__(self, servers):
        self.connections = {server: 0 for server in servers}
    
    def get_server(self):
        return min(self.connections, key=self.connections.get)
    
    def on_connect(self, server):
        self.connections[server] += 1
    
    def on_disconnect(self, server):
        self.connections[server] -= 1
```

**Use when:**
- Connections persist (WebSockets)
- Request durations vary
- Long-lived connections

### 4. Least Response Time
Route to server with lowest latency.

**Use when:**
- Response times vary
- Performance critical
- Monitoring available

### 5. IP Hash
Hash client IP to determine server.

```python
class IPHash:
    def __init__(self, servers):
        self.servers = servers
    
    def get_server(self, client_ip):
        hash_value = hash(client_ip)
        index = hash_value % len(self.servers)
        return self.servers[index]
```

**Pros:**
- Session persistence
- Consistent routing

**Cons:**
- Uneven distribution possible
- Changing servers affects routing

**Use when:**
- Session affinity needed
- No shared session store

### 6. Random
Select random server.

```python
import random

class RandomLB:
    def __init__(self, servers):
        self.servers = servers
    
    def get_server(self):
        return random.choice(self.servers)
```

**Use when:**
- Simple load balancing
- Stateless services

## Load Balancer Types

### 1. Hardware Load Balancers
Physical appliances (F5, Citrix).

**Pros:**
- High performance
- Dedicated resources
- Advanced features

**Cons:**
- Expensive
- Limited flexibility
- Vendor lock-in

### 2. Software Load Balancers
Software-based (HAProxy, NGINX, Envoy).

**Pros:**
- Cost-effective
- Flexible
- Easy to configure

**Cons:**
- Resource overhead
- Requires maintenance

### 3. Cloud Load Balancers
Managed services (AWS ALB/NLB, GCP Load Balancer).

**Pros:**
- Fully managed
- Auto-scaling
- High availability

**Cons:**
- Cost
- Less control
- Cloud vendor lock-in

## Advanced Features

### 1. Health Checks
Monitor server health and remove unhealthy servers.

```nginx
upstream backend {
    server backend1.example.com:8080;
    server backend2.example.com:8080;
    
    # Health check every 5 seconds
    check interval=5000 rise=2 fall=3 timeout=3000;
}
```

**Types:**
- **Active**: LB sends health check requests
- **Passive**: LB monitors actual traffic

### 2. SSL Termination
LB handles SSL, backends use HTTP.

**Benefits:**
- Offload SSL from backends
- Centralized certificate management
- Better performance

```nginx
server {
    listen 443 ssl;
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    
    location / {
        proxy_pass http://backend;
    }
}
```

### 3. Session Persistence (Sticky Sessions)
Route same client to same server.

**Methods:**
- Cookie-based
- IP-based
- SSL session ID

```nginx
upstream backend {
    ip_hash;  # IP-based persistence
    server backend1.example.com;
    server backend2.example.com;
}

# Or cookie-based
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    sticky cookie srv_id expires=1h;
}
```

### 4. Rate Limiting
Limit requests from a client.

```nginx
limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

server {
    location / {
        limit_req zone=mylimit burst=20 nodelay;
        proxy_pass http://backend;
    }
}
```

### 5. Content-Based Routing
Route based on URL, headers, etc.

```nginx
location /api/ {
    proxy_pass http://api_backend;
}

location /static/ {
    proxy_pass http://static_backend;
}

location /admin/ {
    proxy_pass http://admin_backend;
}
```

## Load Balancer Patterns

### 1. Active-Passive
One LB active, one standby.

```
Client → Active LB → Servers
         Passive LB (standby)
```

**Use when:**
- Cost-sensitive
- Simple failover needed

### 2. Active-Active
Multiple LBs handling traffic.

```
Client → DNS → LB1 → Servers
             → LB2 → Servers
```

**Use when:**
- High availability critical
- Need horizontal LB scaling

### 3. Multi-Tier
Load balancers at multiple levels.

```
Client → External LB → Web Tier LBs → Web Servers
                     → App Tier LBs → App Servers
                     → DB LBs → Databases
```

## DNS Load Balancing

Distribute via DNS resolution.

```
example.com → 1.2.3.4
           → 1.2.3.5
           → 1.2.3.6
```

**Pros:**
- Simple
- Geographic distribution
- No single point of failure

**Cons:**
- DNS caching issues
- Slow failover
- No health checks
- Uneven distribution

## Global Server Load Balancing (GSLB)

Route users to nearest data center.

```
User (US) → US Data Center
User (EU) → EU Data Center
User (Asia) → Asia Data Center
```

**Benefits:**
- Lower latency
- Disaster recovery
- Geographic compliance

## Best Practices

1. **Use multiple load balancers**: Avoid single point of failure
2. **Enable health checks**: Remove unhealthy servers automatically
3. **Monitor metrics**: Track latency, error rates, throughput
4. **Use connection pooling**: Reuse connections to backends
5. **Enable compression**: Reduce bandwidth
6. **Configure timeouts**: Prevent hanging connections
7. **Log and analyze**: Track traffic patterns
8. **Test failover**: Regularly test HA setup
9. **Secure LB**: Use firewalls, rate limiting
10. **Plan capacity**: Size for peak + buffer

## Common Issues

### 1. Uneven Load Distribution
**Causes:**
- Poor algorithm choice
- Server capacity mismatch
- Long-lived connections

**Solutions:**
- Use least connections
- Weighted round robin
- Monitor and adjust

### 2. Session Loss
**Causes:**
- No session persistence
- Server failure

**Solutions:**
- Sticky sessions
- Shared session store (Redis)
- Stateless design

### 3. SSL Performance
**Causes:**
- SSL handshake overhead

**Solutions:**
- SSL termination at LB
- Session resumption
- HTTP/2

## Load Balancer Configuration Example (NGINX)

```nginx
http {
    upstream backend {
        least_conn;  # Algorithm
        
        server backend1.example.com:8080 weight=3;
        server backend2.example.com:8080 weight=1;
        server backend3.example.com:8080 backup;
        
        # Health check
        keepalive 32;
    }
    
    server {
        listen 80;
        server_name example.com;
        
        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            
            # Timeouts
            proxy_connect_timeout 5s;
            proxy_send_timeout 10s;
            proxy_read_timeout 10s;
        }
    }
}
```

## Interview Questions

- What's the difference between Layer 4 and Layer 7 load balancing?
- Explain different load balancing algorithms
- How do you handle session persistence?
- What's the difference between active-passive and active-active?
- How do you ensure high availability of load balancers?
- What metrics would you monitor?
