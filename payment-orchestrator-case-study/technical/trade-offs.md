# Technical — Trade-offs

Good architecture is not about eliminating trade-offs.

It is about making them explicit.

---

# PostgreSQL vs NoSQL

### PostgreSQL

Useful when:

- Payment state needs strong consistency
- Relational reporting matters
- Transactions are important
- State transitions need controlled updates

### NoSQL

Useful when:

- Very large distributed workloads require different access patterns
- Flexible schemas are important
- Specific availability/scaling characteristics justify the complexity

### Design choice in this case study

A relational database is the default source of truth for core payment state because payment lifecycle transitions and consistency are central concerns.

---

# Redis vs database for idempotency

### Redis

Pros:

- Fast
- Useful for short-lived coordination

Cons:

- Should not automatically become the only source of durable payment truth

### Database

Pros:

- Durable
- Transactional
- Naturally tied to payment records

Cons:

- More expensive per lookup than in-memory caching

### Design approach

Use the database as the durable authority, with caching only where it improves performance without weakening correctness.

---

# Synchronous vs asynchronous processing

### Synchronous

Useful when the merchant needs an immediate response.

### Asynchronous

Useful for:

- Webhooks
- Event publication
- Reconciliation
- Background recovery

### Design approach

Use synchronous processing for the merchant-facing request where practical, and asynchronous processing for provider callbacks and background workflows.

---

# Retry vs failover

Retrying the same provider is simpler.

Failing over to another provider may improve availability but introduces greater duplicate-processing risk when the original outcome is uncertain.

Therefore:

> Failover is a payment-state decision, not simply an availability decision.

---

# Microservices vs modular monolith

A modular monolith can provide:

- Strong domain boundaries
- Simpler deployment
- Lower operational overhead

Microservices can provide:

- Independent scaling
- Independent deployment
- Stronger infrastructure boundaries

The right choice depends on team size, scale and operational maturity.

For an initial implementation, a modular architecture can keep complexity controlled while preserving clear domain boundaries.

---

# Kafka/event bus vs direct processing

An event bus is useful for:

- Fan-out
- Asynchronous processing
- Decoupling consumers

Direct synchronous processing is simpler for core request paths.

A practical design often uses both.

[Back to README](../README.md)
