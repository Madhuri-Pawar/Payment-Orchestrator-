# 04 — When Things Go Wrong

## In one sentence

The architecture is designed around failure scenarios because payment systems operate across multiple independent systems.

---

# 1. PSP timeout

### What happened?

The orchestrator sent a request but did not receive a response within the expected time.

### Dangerous assumption

```text
Timeout = Failed
```

That can be wrong.

### Safer model

```text
Processing → UNKNOWN
```

Then resolve through:

- Webhook
- Provider status lookup
- Reconciliation

### Business purpose

Avoid making an uncertain payment decision that could create a duplicate charge.

---

# 2. PSP is unavailable

### What happened?

A provider is returning errors or is unavailable.

### System response

The routing layer can mark the provider as temporarily ineligible according to configured health and routing rules.

Where the payment's current state permits it, another eligible PSP may be considered.

### Important constraint

Failover must be **safe for the payment state**.

A system should not blindly send every timeout to another PSP.

---

# 3. Customer sends the same payment twice

### What happened?

The customer double-clicks Pay, or the merchant retries a request.

### System response

Use an idempotency key.

```text
First request
   ↓
Create payment

Repeated request
   ↓
Find existing operation
   ↓
Return existing result
```

---

# 4. Webhook arrives twice

### What happened?

The PSP retries a webhook because it did not receive the expected acknowledgement.

### System response

Store a provider event identity or equivalent deduplication key.

```text
Event #123 → process
Event #123 → duplicate → ignore safely
```

---

# 5. Webhook arrives out of order

### What happened?

Distributed systems do not always deliver events in the order they were generated.

### System response

Apply explicit state-transition rules.

Example:

```text
PROCESSING → SUCCESS     allowed
SUCCESS → PROCESSING     rejected
```

The system may record the rejected event for investigation without corrupting the payment state.

---

# 6. Database is temporarily unavailable

### Risk

The system may receive a payment request but fail to persist the required state.

### Design response

Treat persistence as a critical dependency.

The API should not claim a durable payment creation if the required transaction was not safely committed.

Operationally, the platform should:

- Monitor database health
- Apply bounded retries where appropriate
- Protect the database from retry storms
- Fail requests safely when durable state cannot be established

---

# 7. Event/message delivery fails

If asynchronous events are used, the system should avoid losing important state changes.

A common pattern is the **transactional outbox**:

```text
Database transaction
    |
    +--> Payment state update
    |
    +--> Outbox event
             |
             v
        Event publisher
             |
             v
          Consumers
```

The payment update and the intent to publish the event are committed together.

---

# 8. Payment gets stuck

Example:

```text
PROCESSING
    |
    | no final result
    |
    v
Still PROCESSING
```

The platform needs operational detection.

Possible controls:

- Age-based monitoring
- Alerts
- Recovery jobs
- Provider status checks
- Reconciliation

---

# 9. Internal state differs from provider state

Example:

```text
Internal system: UNKNOWN
PSP: SUCCESS
```

This is a reconciliation case.

The system should be able to identify the mismatch and move the internal record toward the correct state using a controlled process.

---

# Failure-handling principle

> **Do not hide uncertainty. Model it, observe it, and resolve it.**

This principle is more important than any individual retry mechanism.

**Next:** [Business Value →](05-business-value.md)

[Back to README](README.md)
