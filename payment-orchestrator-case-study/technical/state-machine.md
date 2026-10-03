# Technical — Payment State Machine

## Why a state machine?

Payment status must not be arbitrary.

The system needs explicit rules for:

- What states exist
- Which transitions are allowed
- Which events can cause transitions
- How ambiguous outcomes are represented

---

# Core states

```text
CREATED
   ↓
PROCESSING
   ↓
 ┌───────────────┬───────────────┐
 ↓               ↓               ↓
SUCCESS        FAILED         UNKNOWN
                                   |
                                   v
                              RECONCILIATION
                               /          \
                              /            \
                             v              v
                          SUCCESS         FAILED
```

---

# Example transition matrix

| Current state | Event | Next state |
|---|---|---|
| CREATED | processing started | PROCESSING |
| PROCESSING | provider success | SUCCESS |
| PROCESSING | provider explicit failure | FAILED |
| PROCESSING | ambiguous timeout | UNKNOWN |
| UNKNOWN | confirmed success | SUCCESS |
| UNKNOWN | confirmed failure | FAILED |

---

# Invalid examples

These should normally be rejected by the state-transition layer:

```text
SUCCESS → PROCESSING
SUCCESS → CREATED
FAILED → PROCESSING
```

Whether a particular transition is allowed can depend on the exact business model, but it should always be explicit.

---

# Why UNKNOWN exists

UNKNOWN means:

> The platform cannot yet establish the final outcome with sufficient confidence.

It is not merely another word for failure.

This state allows the platform to pause irreversible decisions until more information arrives.

---

# State transition ownership

Only controlled domain logic should change payment state.

Avoid scattered code such as:

```text
controller changes status
webhook handler changes status
retry worker changes status
reconciliation job changes status
```

Instead:

```text
Event
  ↓
State transition service
  ↓
Validate
  ↓
Persist
  ↓
Audit
```

[Back to README](../README.md)
