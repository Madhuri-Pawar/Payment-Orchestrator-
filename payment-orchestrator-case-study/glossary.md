# Glossary

This page is for readers who do not work in payment engineering every day.

---

| Term | Plain-English meaning |
|---|---|
| PSP | Payment Service Provider — a company that provides payment-processing infrastructure |
| Payment Orchestrator | A system that coordinates payments across multiple PSPs |
| Routing | Deciding which PSP should receive a payment |
| Failover | Using another provider when conditions allow it |
| Idempotency | Making a repeated request safe so it does not create duplicate work |
| Webhook | A notification sent by one system to another when an event happens |
| State Machine | A controlled list of states and allowed transitions |
| UNKNOWN | The system cannot yet determine the final payment outcome |
| Reconciliation | Comparing records between systems to find mismatches |
| Adapter | A software layer that translates between two different APIs |
| Retry | Trying an operation again after a failure |
| Circuit Breaker | A mechanism that temporarily stops calls to an unhealthy dependency |
| Outbox | A database-backed pattern for reliably publishing events |
| Observability | Metrics, logs and traces that help teams understand system behaviour |
| HLD | High-Level Design |
| LLD | Low-Level Design |
| Audit Trail | Historical records showing important actions and decisions |
| Provider | Usually a PSP or other external payment service |
| Payment Attempt | One provider-level processing attempt for a payment |
| Reconciliation Job | A process that compares internal and provider records |

---

## The three terms worth remembering

### Idempotency

> "If I accidentally send the same request twice, don't accidentally create two payments."

### UNKNOWN

> "I don't know the final outcome yet, so don't pretend I do."

### Reconciliation

> "Let's compare our records with the provider's records and resolve differences."

[Back to README](README.md)
