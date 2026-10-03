# 05 — Business Value

## In one sentence

The Payment Orchestrator turns payment-provider complexity into a controlled platform capability.

---

# What the business gets

## 1. Provider flexibility

The merchant integrates with one platform rather than building every provider integration independently.

```text
Merchant
   |
   v
Orchestrator
   |
   +--- PSP A
   +--- PSP B
   +--- PSP C
```

---

## 2. Better failure management

Failures are handled as explicit scenarios instead of being scattered across application code.

Examples:

- Timeout
- Provider outage
- Duplicate request
- Duplicate webhook
- Unknown outcome
- Reconciliation mismatch

---

## 3. Consistent payment behaviour

The merchant gets one payment model even when providers differ internally.

That means the merchant does not need to understand every provider's:

- API shape
- Error model
- Webhook format
- Status naming
- Retry behaviour

---

## 4. Operational visibility

A platform team can answer questions such as:

- How many payments are processing?
- How many are UNKNOWN?
- Which provider is producing errors?
- Which payment methods are affected?
- Which payments need reconciliation?
- Which events are failing?

---

# What can be measured

This architecture creates a foundation for metrics such as:

### Payment metrics

- Payment success rate
- Payment failure rate
- UNKNOWN payment count
- Processing latency
- Provider-specific error rate

### Reliability metrics

- PSP availability
- Retry volume
- Failover volume
- Webhook processing failures
- Reconciliation mismatch count

### Operational metrics

- Stuck payment count
- Alert volume
- Recovery time
- Investigation backlog

These are **measurement categories**, not claims about actual production performance.

---

# Business-to-technical mapping

| Business concern | Architecture capability |
|---|---|
| Provider dependency | Multi-PSP orchestration |
| Duplicate charges | Idempotency |
| Uncertain outcomes | UNKNOWN state |
| Provider differences | PSP adapters |
| Delayed updates | Webhook processing |
| Data mismatch | Reconciliation |
| Incident investigation | Observability + audit |
| Increasing volume | Horizontal scaling strategy |

---

# What this architecture does not promise

It does not promise:

- Zero payment failures
- Zero provider outages
- Zero operational incidents
- Automatic recovery from every ambiguous case

Instead, it provides a structured way to **detect, contain, investigate and recover from payment failures**.

---

# The value proposition

The core business value is:

```text
Payment complexity
       ↓
Centralised orchestration
       ↓
Controlled provider interaction
       ↓
Better reliability + visibility
```

**Next:** [Glossary →](glossary.md)

[Back to README](README.md)
