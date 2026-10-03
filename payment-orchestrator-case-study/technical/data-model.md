# Technical — Data Model

## Core entities

```mermaid
erDiagram
    MERCHANT ||--o{ PAYMENT : owns
    PAYMENT ||--o{ PAYMENT_ATTEMPT : has
    PAYMENT ||--o{ WEBHOOK_EVENT : receives
    PAYMENT ||--o{ AUDIT_EVENT : records
    PAYMENT ||--o{ IDEMPOTENCY_KEY : references

    MERCHANT {
        uuid id PK
        string name
        string status
        timestamp created_at
    }

    PAYMENT {
        uuid id PK
        uuid merchant_id FK
        string amount
        string currency
        string payment_method
        string status
        string idempotency_key
        timestamp created_at
        timestamp updated_at
    }

    PAYMENT_ATTEMPT {
        uuid id PK
        uuid payment_id FK
        string provider
        string provider_reference
        string status
        string error_code
        timestamp started_at
        timestamp completed_at
    }

    WEBHOOK_EVENT {
        uuid id PK
        uuid payment_id FK
        string provider
        string event_id
        string event_type
        string processing_status
        timestamp received_at
    }

    IDEMPOTENCY_KEY {
        uuid id PK
        uuid payment_id FK
        string merchant_id
        string key_value
        string request_hash
        timestamp created_at
    }

    AUDIT_EVENT {
        uuid id PK
        uuid payment_id FK
        string event_type
        string actor
        string metadata
        timestamp created_at
    }
```

---

# Payment table

The payment represents the **business-level payment operation**.

Important fields:

```text
payment_id
merchant_id
amount
currency
payment_method
status
idempotency_key
created_at
updated_at
```

---

# Attempt table

An attempt represents interaction with a specific PSP.

This distinction is important.

```text
Payment
  |
  +-- PSP A attempt
  +-- PSP B attempt
```

The payment may have one or multiple attempts while still representing one logical customer payment.

---

# Webhook event table

Used for:

- Deduplication
- Audit
- Operational investigation
- Reprocessing controls

A provider event identifier should be uniquely constrained within the relevant provider scope.

---

# Idempotency constraints

Conceptually:

```text
UNIQUE(merchant_id, key_value)
```

A request hash can be stored to detect the same key being reused for a different request.

---

# Audit events

Audit records should answer:

> Who/what changed the payment, when, and why?

Examples:

```text
PAYMENT_CREATED
ROUTING_SELECTED
ATTEMPT_CREATED
STATE_CHANGED
WEBHOOK_RECEIVED
RECONCILIATION_UPDATED
```

[Back to README](../README.md)
