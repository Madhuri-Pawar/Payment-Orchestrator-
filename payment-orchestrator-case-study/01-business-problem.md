# 01 — The Business Problem

## In one sentence

A merchant wants reliable payment processing without becoming tightly dependent on a single payment provider.

---

## Imagine the business

A growing online merchant accepts payments from customers.

At first, connecting to one PSP is simple:

```text
Merchant → PSP → Customer payment
```

As transaction volume and business requirements grow, the merchant may want multiple PSPs.

Why?

Because payment providers can differ in:

- Availability
- Supported payment methods
- Geographic coverage
- Processing performance
- Commercial terms
- Operational behaviour

But introducing multiple PSPs also introduces complexity.

---

# The real problem

The merchant does not want to build this:

```text
Order Service
   |
   +--> PSP A integration
   |
   +--> PSP B integration
   |
   +--> PSP C integration
```

Now every business service needs to understand every provider.

That creates duplicated logic and provider-specific complexity.

A better model is:

```text
Order Service
      |
      v
Payment Orchestrator
   /      |      \
PSP A   PSP B   PSP C
```

The orchestrator becomes the single payment integration layer.

---

# Why failure is harder than success

The happy path is easy:

```text
Request → PSP → SUCCESS
```

The difficult cases are ambiguous.

### Example

The orchestrator sends a payment to PSP A.

PSP A processes it.

Then the network connection breaks before the response reaches the orchestrator.

The orchestrator sees:

```text
TIMEOUT
```

But reality could be:

```text
Payment = SUCCESS
```

If the system immediately sends the payment to PSP B, the customer could potentially be charged twice.

Therefore the architecture must distinguish:

```text
FAILED
```

from:

```text
UNKNOWN
```

---

# Business impact of poor design

A weak payment architecture can create:

### Customer impact

- Confusing payment status
- Repeated payment attempts
- Potential duplicate charges
- Poor checkout experience

### Merchant impact

- Lost transactions
- Operational investigation
- Reconciliation work
- Provider dependency
- Difficult incident handling

### Engineering impact

- Multiple provider integrations
- Duplicated retry logic
- Difficult state management
- Hard-to-debug asynchronous events

---

# Desired future state

The business wants:

```text
                    +--> PSP A
                    |
Merchant → Orchestrator +--> PSP B
                    |
                    +--> PSP C
```

with the orchestrator responsible for:

1. Creating a payment identity
2. Selecting a provider
3. Sending the payment safely
4. Handling provider responses
5. Processing webhooks
6. Managing payment state
7. Handling retries where safe
8. Recording an audit trail
9. Supporting reconciliation
10. Exposing operational visibility

---

# Key business requirements

| Requirement | Why it matters |
|---|---|
| Multiple PSPs | Reduce dependency on one provider |
| Reliable payment state | Know what happened to each payment |
| Idempotency | Prevent duplicate processing |
| Safe retries | Recover from transient failures |
| Webhooks | Receive asynchronous provider updates |
| Reconciliation | Detect mismatches between systems |
| Observability | Detect and investigate incidents |
| Auditability | Understand important decisions later |

---

# What success means

The goal is not simply:

> "Build an API that calls PSPs."

The real goal is:

> **Build a controlled payment decision and state-management layer that protects the merchant and customer from provider complexity and distributed-system failures.**

---

## Simple glossary

- **PSP:** Payment Service Provider.
- **Orchestrator:** The system coordinating payment processing across providers.
- **Idempotency:** Repeating the same request safely without creating a duplicate payment.
- **Webhook:** A provider-to-platform notification about an event.
- **Reconciliation:** Comparing records between systems to identify mismatches.
- **Failover:** Moving processing to another provider when conditions allow it.

**Next:** [How the Solution Works →](02-how-the-solution-works.md)

[Back to README](README.md)
