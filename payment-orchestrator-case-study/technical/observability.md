# Technical — Observability

## The operational question

When a payment fails, the team needs to answer:

> What happened, where did it happen, and what should we do next?

---

# Metrics

## Payment metrics

```text
payment_attempts_total
payment_success_total
payment_failed_total
payment_unknown_total
payment_processing_duration
```

## Provider metrics

```text
provider_request_total
provider_error_total
provider_timeout_total
provider_latency
provider_success_rate
```

## Webhook metrics

```text
webhook_received_total
webhook_duplicate_total
webhook_invalid_total
webhook_processing_failure_total
```

## Reconciliation metrics

```text
reconciliation_mismatch_total
reconciliation_resolved_total
```

---

# Logs

Logs should contain correlation identifiers such as:

```text
request_id
payment_id
attempt_id
provider
provider_reference
```

Avoid logging sensitive information unnecessarily.

---

# Distributed tracing

A trace can follow:

```text
Merchant request
   ↓
Payment API
   ↓
Routing
   ↓
PSP adapter
   ↓
Provider request
   ↓
Webhook
```

This is particularly useful when a payment crosses multiple services.

---

# Alerts

Examples:

- Provider timeout rate exceeds configured threshold
- UNKNOWN payments accumulate
- Webhook processing failures increase
- Reconciliation mismatch count rises
- Database error rate increases
- Queue backlog grows

Thresholds should be calibrated to actual production traffic rather than copied from this case study.

---

# Operational dashboard

A useful dashboard might show:

```text
Payments
├── Success
├── Failed
├── Processing
└── Unknown

Provider health
├── PSP A
├── PSP B
└── PSP C

Operations
├── Webhook failures
├── Reconciliation mismatches
└── Queue backlog
```

[Back to README](../README.md)
