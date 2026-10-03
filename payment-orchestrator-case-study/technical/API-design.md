# Technical — API Design

## API philosophy

The merchant should see a stable, provider-neutral API.

Provider-specific fields belong behind the adapter boundary wherever possible.

---

# Create payment

```http
POST /v1/payments
Idempotency-Key: <unique-key>
Content-Type: application/json
```

Example request:

```json
{
  "amount": 1000,
  "currency": "INR",
  "payment_method": "UPI",
  "merchant_reference": "ORDER-123"
}
```

Example response:

```json
{
  "payment_id": "pay_12345",
  "status": "PROCESSING"
}
```

---

# Get payment

```http
GET /v1/payments/{payment_id}
```

Example:

```json
{
  "payment_id": "pay_12345",
  "status": "SUCCESS",
  "amount": 1000,
  "currency": "INR"
}
```

---

# Webhook endpoint

```http
POST /v1/webhooks/{provider}
```

The endpoint should:

1. Authenticate/verify the provider message
2. Identify the event
3. Deduplicate
4. Map provider state to canonical state
5. Apply the state transition
6. Record the event
7. Return an appropriate acknowledgement

---

# Error model

A consistent error model helps merchants handle failures safely.

Example:

```json
{
  "error": {
    "code": "PAYMENT_NOT_FOUND",
    "message": "Payment could not be found",
    "request_id": "req_123"
  }
}
```

Avoid exposing internal provider details unnecessarily.

---

# Idempotency behaviour

A repeated request with the same key should return the existing operation result where the request is equivalent.

A reused key with a materially different payload should produce a deterministic client error.

---

# API design principle

> Stable external contract, flexible internal provider integrations.

[Back to README](../README.md)
