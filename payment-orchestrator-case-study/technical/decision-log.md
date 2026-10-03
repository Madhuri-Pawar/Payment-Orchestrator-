# Technical — Architecture Decision Log

## ADR-001 — Centralise PSP integrations

**Decision:** Use a Payment Orchestrator as the provider integration boundary.

**Reason:** Prevent provider-specific complexity from spreading across merchant services.

---

## ADR-002 — Model UNKNOWN explicitly

**Decision:** Use UNKNOWN for ambiguous payment outcomes.

**Reason:** A timeout does not prove that the provider did not process the payment.

---

## ADR-003 — Separate payment from payment attempt

**Decision:** Store one logical payment and one or more provider attempts.

**Reason:** A single customer payment can interact with multiple providers while remaining one business operation.

---

## ADR-004 — Use explicit state transitions

**Decision:** Payment states change through controlled transition logic.

**Reason:** Prevent invalid or out-of-order updates from corrupting the lifecycle.

---

## ADR-005 — Treat webhooks as at-least-once events

**Decision:** Make webhook processing idempotent.

**Reason:** Providers may retry delivery.

---

## ADR-006 — Keep provider logic behind adapters

**Decision:** Use a canonical payment model and provider adapters.

**Reason:** Isolate provider-specific API differences.

---

## ADR-007 — Make reconciliation a first-class capability

**Decision:** Include reconciliation in the architecture rather than treating it as an operational afterthought.

**Reason:** Distributed payment systems can temporarily disagree even when each system is functioning as designed.

[Back to README](../README.md)
