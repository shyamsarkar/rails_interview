# 036 - Event-Driven Architecture Basics

## 1. What is Event-Driven Architecture?
Event-Driven Architecture (EDA) is a design pattern where services communicate by producing and reacting to events instead of calling each other directly.

- An event is a meaningful change in state.
- Example: `OrderPlaced`, `PaymentSucceeded`, `InventoryReserved`.

## 2. Core Concepts
- Event: A notification that something happened.
- Producer: The service that creates and publishes an event.
- Consumer: The service that listens for and processes the event.
- Event Broker: The middleware that transports events.
- Topic / Queue: The channel through which events are delivered.
- Subscription: A consumer’s interest in a specific event type.

## 3. How It Works
1. A service detects a change.
2. It publishes an event to a broker.
3. One or more consumers subscribe to that event.
4. Consumers process the event asynchronously.

Example flow:
- Order service publishes `OrderCreated`
- Billing service consumes it and charges the customer
- Inventory service consumes it and reserves stock

## 4. Key Characteristics
- Loose coupling between services
- Asynchronous communication
- Scalability and flexibility
- Better support for real-time features

## 5. Advantages
- Decouples services and reduces direct dependencies
- Improves scalability because producers and consumers can scale independently
- Supports real-time processing and event streaming
- Increases resilience through asynchronous workflows

## 6. Challenges
- Eventual consistency instead of immediate consistency
- Duplicate event processing
- Ordering issues in some systems
- Harder debugging and monitoring
- Need for idempotency and retry mechanisms

## 7. Event-Driven vs Request-Driven
- Request-driven: one service calls another and waits for a response
- Event-driven: one service publishes an event and other services react later

Request-driven is simpler but creates tighter coupling.  
Event-driven is more flexible but adds complexity.

## 8. Common Patterns
- Pub/Sub: one event is delivered to multiple subscribers
- Event Streaming: events are stored and processed in order
- Event Sourcing: state changes are captured as a sequence of events
- CQRS: separate read and write models for better scalability

## 9. Example
```text
User places order
   ↓
Order service publishes OrderPlaced
   ↓
Billing service handles payment
Inventory service updates stock
Notification service sends email
```

## 10. Interview Talking Points
- EDA is based on events, not direct service calls
- It helps with loose coupling and asynchronous processing
- You must handle retries, duplicates, and ordering
- It is great for microservices and real-time systems

## 11. Summary
Event-Driven Architecture is useful when systems need to react to changes quickly, scale independently, and remain loosely coupled. It is powerful, but it requires careful design around reliability and consistency.