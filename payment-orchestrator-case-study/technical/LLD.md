# Technical — Low-Level Design

## Purpose

Describe the internal behaviour of payment creation, idempotency, routing, provider attempts and webhook processing.

---

# 1. Payment creation

Conceptual sequence:

```text
POST /payments
      |
      v
Validate request
      |
      v
Validate idempotency key
      |
      v
Lookup existing key
      |
   +--+--+
   |     |
Found   New
   |     |
Return  Create payment
existing   |
result     v
         Route
```

---

# 2. Idempotency model

An idempotency record can contain:

```text
merchant_id
idempotency_key
request_hash
payment_id
response_snapshot
status
created_at
expires_at
```

Important rules:

1. The key must be scoped to the merchant.
2. The same key with a different request payload should be rejected.
3. The same key with the same request should resolve to the same logical operation.
4. The persistence operation must be concurrency-safe.

---

# 3. Routing model

A routing decision can evaluate:

```text
merchant
currency
country
payment_method
provider eligibility
provider health
configured priority
capacity / rate limits
```

Example conceptual algorithm:

```text
eligible_providers = filter(providers, payment_is_supported)

ranked_providers = apply_routing_rules(eligible_providers)

selected_provider = first_eligible(ranked_providers)
```

Routing policy should be configuration-driven rather than hard-coded throughout the payment service.

---

# 4. PSP adapter contract

The core system should expose a canonical interface such as:

```text
createPayment()
getPaymentStatus()
refundPayment()
verifyWebhook()
parseWebhook()
```

Each adapter maps the canonical contract to a provider-specific API.

---

# 5. Attempt model

A payment and a provider attempt are different concepts.

```text
Payment
  |
  +-- Attempt 1 → PSP A
  |
  +-- Attempt 2 → PSP B
```

The attempt records provider-specific details such as:

- provider
- provider payment reference
- request timestamp
- response timestamp
- provider status
- error code
- attempt status

This makes the payment lifecycle auditable.

---

# 6. Safe retry principle

Retries are not automatically safe.

The decision should consider:

```text
Was the request definitely rejected?
Was the request definitely never sent?
Could the provider have processed it?
Is the operation idempotent?
Is there a provider reference?
```

A timeout after submission is materially different from a connection failure before the provider received the request.

---

# 7. Webhook processing

Conceptual algorithm:

```text
Receive webhook
      |
      v
Verify authenticity
      |
      v
Extract event identity
      |
      v
Check duplicate
      |
   +--+--+
   |     |
Seen   New
   |     |
Ack    Load payment
          |
          v
      Validate transition
          |
          v
       Update state
          |
          v
       Record event
```

---

# 8. Concurrency

Potential race:

```text
Webhook A: SUCCESS
Webhook B: FAILED
```

Both arrive concurrently.

The database transaction must protect the payment state from invalid transitions.

Options include:

- Row-level locking
- Optimistic concurrency
- Version columns
- Transactional state-transition validation

---

# 9. State-transition pseudologic

```text
if current_state == PROCESSING
   and incoming_state == SUCCESS:
       allow

if current_state == SUCCESS
   and incoming_state == PROCESSING:
       reject
```

The actual transition matrix belongs in [State Machine](state-machine.md).

[Back to README](../README.md)
