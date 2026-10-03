# Technical — Reliability Strategy

## Reliability goals

The system should:

- Avoid duplicate payment processing
- Preserve payment state
- Handle provider failures
- Make uncertainty visible
- Recover from transient failures
- Support operational investigation

---

# 1. Idempotency

Use idempotency at the merchant-facing API boundary.

Use separate deduplication controls for provider webhooks.

---

# 2. Timeouts

Every external call should have an explicit timeout.

Do not allow provider calls to consume application threads indefinitely.

---

# 3. Retries

Retries should be:

- Bounded
- Delayed
- Context-aware
- Observable

Avoid:

```text
retry immediately
retry immediately
retry immediately
```

Prefer controlled backoff with limits.

---

# 4. Retry vs failover

These are different decisions.

### Retry

Try the same provider again.

### Failover

Consider another provider.

Failover requires stronger safety checks because the first provider may have processed the original request.

---

# 5. Circuit breaker

A circuit breaker can prevent the platform from repeatedly sending traffic to a known unhealthy dependency.

Conceptually:

```text
CLOSED
  |
  | repeated failures
  v
OPEN
  |
  | recovery test
  v
HALF-OPEN
  |
  +--> healthy → CLOSED
  |
  +--> unhealthy → OPEN
```

---

# 6. Transactional outbox

When a state change must produce an internal event:

```text
BEGIN
  update payment
  insert outbox event
COMMIT
```

A publisher later delivers the outbox event.

This reduces the risk of:

```text
DB updated
but
event lost
```

---

# 7. Recovery

Recovery mechanisms can include:

- Reconciliation jobs
- Stuck-payment detection
- Provider status queries
- Dead-letter queues
- Operational replay controls

---

# Reliability principle

> Make failures explicit, bounded, observable and recoverable.

[Back to README](../README.md)
