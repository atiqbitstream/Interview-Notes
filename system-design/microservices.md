# Microservices Architecture

## What are Microservices?

Microservices is an architectural style where an application is composed of small, independent services that communicate over network protocols.

## Monolith vs Microservices

### Monolithic Architecture
Single, unified codebase with all functionality.

```
[User Management + Orders + Payments + Shipping + Inventory]
```

**Pros:**
- Simple to develop initially
- Easy to test
- Simple deployment
- No network overhead
- Easier debugging

**Cons:**
- Hard to scale specific features
- Technology lock-in
- Large codebase
- Long deployment cycles
- Team coordination issues

### Microservices Architecture
Multiple independent services.

```
[User Service] [Order Service] [Payment Service] [Shipping Service] [Inventory Service]
```

**Pros:**
- Independent scaling
- Technology flexibility
- Faster deployment
- Team autonomy
- Fault isolation
- Easier to understand (small services)

**Cons:**
- Distributed system complexity
- Network latency
- Data consistency challenges
- Testing complexity
- Operational overhead
- More resources needed

## When to Use Microservices

### Use Microservices When:
- Application is large and complex
- Different parts need different scaling
- Multiple teams working on app
- Need technology flexibility
- Frequent deployments needed
- Parts have different SLAs

### Stick with Monolith When:
- Small application
- Small team
- Starting out
- Simple domain
- Tight coupling needed
- Limited operational capacity

## Microservices Principles

### 1. Single Responsibility
Each service does one thing well.

```
✓ Order Service: Manages orders
✗ Order + Payment + Shipping Service: Too broad
```

### 2. Independently Deployable
Deploy services without affecting others.

### 3. Decentralized Data Management
Each service owns its data.

```
User Service → User DB
Order Service → Order DB
Payment Service → Payment DB
```

### 4. API-First Design
Well-defined interfaces between services.

```
POST /api/v1/orders
GET /api/v1/orders/{id}
PUT /api/v1/orders/{id}
DELETE /api/v1/orders/{id}
```

### 5. Failure Isolation
Failure in one service doesn't crash others.

## Service Communication

### 1. Synchronous (REST, gRPC)

**REST API:**
```python
# Order Service calls Payment Service
response = requests.post(
    'http://payment-service/api/v1/payments',
    json={'amount': 100, 'order_id': '123'}
)
```

**Pros:**
- Simple
- Immediate response
- Easy to debug

**Cons:**
- Tight coupling
- Cascading failures
- Lower availability

### 2. Asynchronous (Message Queue, Events)

**Message Queue:**
```python
# Order Service publishes event
message_queue.publish('order.created', {
    'order_id': '123',
    'user_id': '456',
    'total': 100
})

# Payment Service subscribes
def handle_order_created(message):
    process_payment(message['order_id'], message['total'])

message_queue.subscribe('order.created', handle_order_created)
```

**Pros:**
- Loose coupling
- Better fault tolerance
- Can handle traffic spikes

**Cons:**
- Complex
- Eventual consistency
- Harder to debug

### 3. Event-Driven Architecture
Services communicate via events.

```
Order Created → [Inventory Updated, Payment Processed, Email Sent, Analytics Updated]
```

## Data Management Patterns

### 1. Database per Service
Each service has its own database.

```
User Service → PostgreSQL
Order Service → MongoDB
Analytics Service → Elasticsearch
```

**Pros:**
- Independence
- Technology choice
- Easier scaling

**Cons:**
- Distributed transactions
- Data duplication
- Complex queries

### 2. Saga Pattern
Manage distributed transactions.

**Choreography (Event-Based):**
```
Order Service → Order Created Event
              → Payment Service → Payment Processed Event
                                → Shipping Service → Shipped Event
```

**Orchestration (Centralized):**
```
Order Saga Orchestrator:
  1. Create Order → Order Service
  2. Process Payment → Payment Service
  3. Update Inventory → Inventory Service
  4. Arrange Shipping → Shipping Service
```

### 3. CQRS (Command Query Responsibility Segregation)
Separate read and write models.

```
Write Model (Commands) → PostgreSQL
Read Model (Queries) → Elasticsearch (optimized for reads)
```

### 4. Event Sourcing
Store events instead of current state.

```
Events:
  - AccountCreated(id=1, balance=0)
  - MoneyDeposited(id=1, amount=100)
  - MoneyWithdrawn(id=1, amount=30)

Current State: balance = 70
```

## Service Discovery

### 1. Client-Side Discovery
Client queries service registry.

```python
# Client
def call_order_service():
    instances = service_registry.get('order-service')
    instance = load_balancer.choose(instances)
    return http_client.get(f"{instance.url}/orders")
```

### 2. Server-Side Discovery
Load balancer queries service registry.

```
Client → Load Balancer → Service Registry → Service Instances
```

**Tools:**
- Consul
- Eureka
- etcd
- Zookeeper

## API Gateway

Single entry point for all clients.

```
Client → API Gateway → [User Service, Order Service, Payment Service]
```

**Responsibilities:**
- Request routing
- Authentication/Authorization
- Rate limiting
- Load balancing
- Caching
- Request/response transformation

**Example (Node.js):**
```javascript
// API Gateway
app.get('/api/user/:id', async (req, res) => {
    const user = await userService.getUser(req.params.id);
    const orders = await orderService.getUserOrders(req.params.id);
    res.json({ user, orders });
});
```

## Resilience Patterns

### 1. Circuit Breaker
Stop calling failing service.

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failure_count = 0
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.state = 'CLOSED'
        self.last_failure_time = None
    
    def call(self, func):
        if self.state == 'OPEN':
            if time.time() - self.last_failure_time > self.timeout:
                self.state = 'HALF_OPEN'
            else:
                raise Exception("Circuit breaker is OPEN")
        
        try:
            result = func()
            if self.state == 'HALF_OPEN':
                self.state = 'CLOSED'
                self.failure_count = 0
            return result
        except Exception as e:
            self.failure_count += 1
            self.last_failure_time = time.time()
            if self.failure_count >= self.failure_threshold:
                self.state = 'OPEN'
            raise e
```

### 2. Retry Pattern
Retry failed requests.

```python
@retry(max_attempts=3, backoff=exponential)
def call_payment_service():
    return requests.post('http://payment-service/api/charge')
```

### 3. Timeout
Don't wait forever.

```python
response = requests.get('http://service/api', timeout=5)
```

### 4. Bulkhead
Isolate resources.

```python
# Separate thread pools for different services
user_pool = ThreadPoolExecutor(max_workers=10)
order_pool = ThreadPoolExecutor(max_workers=20)
```

### 5. Rate Limiting
Limit requests to service.

```python
@rate_limit(requests_per_minute=100)
def api_endpoint():
    return process_request()
```

## Observability

### 1. Logging
Centralized logging across services.

```python
import logging

logger = logging.getLogger(__name__)
logger.info("Order created", extra={
    'order_id': '123',
    'user_id': '456',
    'service': 'order-service'
})
```

**Tools:** ELK Stack, Splunk, Datadog

### 2. Metrics
Monitor service health.

```python
from prometheus_client import Counter, Histogram

requests_total = Counter('requests_total', 'Total requests')
request_duration = Histogram('request_duration_seconds', 'Request duration')

@request_duration.time()
def process_order():
    requests_total.inc()
    # Process order
```

**Metrics to track:**
- Request rate
- Error rate
- Latency (P50, P95, P99)
- Resource usage

### 3. Distributed Tracing
Track requests across services.

```python
from opentelemetry import trace

tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("process_order"):
    create_order()
    with tracer.start_as_current_span("charge_payment"):
        payment_service.charge()
    with tracer.start_as_current_span("update_inventory"):
        inventory_service.update()
```

**Tools:** Jaeger, Zipkin, AWS X-Ray

## Security

### 1. Authentication
Verify identity.

```
Client → API Gateway (JWT validation) → Services
```

### 2. Authorization
Verify permissions.

```python
@require_permission('order:create')
def create_order():
    # Create order
```

### 3. Service-to-Service Auth
Mutual TLS, service mesh.

### 4. Secrets Management
Store credentials securely.

**Tools:** HashiCorp Vault, AWS Secrets Manager

## Deployment

### 1. Containerization
Package services as containers.

```dockerfile
FROM python:3.9
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### 2. Orchestration
Manage containers at scale.

```yaml
# Kubernetes deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: order-service
        image: order-service:v1
        ports:
        - containerPort: 8080
```

**Tools:** Kubernetes, Docker Swarm, ECS

### 3. CI/CD
Automate deployment.

```
Code Push → Build → Test → Deploy to Staging → Deploy to Production
```

## Best Practices

1. Start with monolith, evolve to microservices
2. Define clear service boundaries
3. Use API versioning
4. Implement comprehensive monitoring
5. Automate everything
6. Design for failure
7. Use asynchronous communication when possible
8. Implement proper security
9. Document APIs
10. Keep services small and focused

## Common Pitfalls

1. Too many microservices
2. Distributed monolith
3. Ignoring network latency
4. Poor monitoring
5. Inadequate testing
6. Not planning for failures
7. Over-engineering
8. Ignoring operational complexity

## Interview Questions

- When would you choose microservices over monolith?
- How do microservices communicate?
- Explain the Saga pattern
- What is service discovery?
- How do you handle distributed transactions?
- Explain circuit breaker pattern
- How do you monitor microservices?
