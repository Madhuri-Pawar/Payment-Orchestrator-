# Technical — Interview Discussion

This document turns the case study into architecture discussion topics.

---

# 1. Why do we need an orchestrator?

Because multiple PSP integrations create duplicated provider-specific logic if each merchant service owns its own integration.

---

# 2. Why is UNKNOWN important?

Because a timeout tells us about communication, not necessarily about the payment's final outcome.

---

# 3. Why can't we retry every timeout?

Because the first provider may already have processed the payment.

---

# 4. Why separate payment and attempt?

Because one logical payment may have multiple provider interactions.

---

# 5. Why are webhooks idempotent?

Because providers may deliver the same event more than once.

---

# 6. How do you prevent out-of-order events?

Use an explicit state-transition model with transactional concurrency control.

---

# 7. What happens if the database is down?

The platform should avoid claiming durable payment creation when it cannot safely persist the required state.

---

# 8. What happens if the provider is down?

Routing and provider-health controls can make the provider temporarily ineligible, while payment-state rules determine whether another provider can safely be used.

---

# 9. What would you monitor first?

A practical starting set:

- Payment success/failure
- Provider latency/errors/timeouts
- UNKNOWN payments
- Webhook failures
- Reconciliation mismatches
- Database health
- Queue backlog

---

# 10. What would you improve next?

Possible future areas:

- More sophisticated routing
- Provider-specific health scoring
- Advanced reconciliation
- Multi-region architecture
- Capacity modelling
- Fraud/risk integration
- Merchant configuration platform
- Cost-aware routing

These are extensions, not assumptions about the current design.

[Back to README](../README.md)
