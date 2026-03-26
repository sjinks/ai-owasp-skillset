---
name: security-review-owasp-third-party-payment-gateway-integration
description: Review payment order validation, callback authentication, idempotency, and fraud detection against OWASP payment gateway guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Third-Party Payment Gateway Integration Security Review

Apply this skill to security reviews focused on Third-Party Payment Gateway Integration concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on client-side price tampering, spoofed payment callbacks, replayed transactions, premature fulfillment, and weak fraud telemetry in third-party payment integrations. Prefer precise findings over broad advice.

## Scope
- server-side order creation, amount calculation, product validation, and payment initialization before redirect or token issuance
- callback, webhook, redirect-return, and server-to-server verification flows used to confirm payment state
- idempotency, transaction replay resistance, fulfillment sequencing, and fraud logging around payment events

- code paths, configuration, infrastructure, tests, and workflows related to this topic
- trust boundaries, dependencies, integrations, and operational controls influenced by this topic
- documentation or security assumptions that materially affect how the controls are implemented

## Review Goals
- Find places where attackers can alter order details, spoof payment success, replay transactions, or trigger fulfillment without verified payment state.
- Prioritize callback trust, server-side amount verification, and idempotent fulfillment over lower-impact dashboard or reporting gaps.

- Find weaknesses and missing controls that materially affect the security properties of this topic.
- Prioritize issues that are exploitable, exposed to untrusted input, or central to the trust boundaries in scope.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the order creation, payment handoff, callback, verification, and fulfillment steps in scope.
2. Trace what data is trusted from the client, what is trusted from the gateway, and what is independently verified by the merchant system.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they permit financial loss, unauthorized fulfillment, or payment-state forgery.

## Review Checklist

### Order Initialization and Price Integrity
- Check that product IDs, prices, discounts, tax, and currency are recalculated on the server from trusted data.
- Check that client-supplied basket or order data cannot directly define what gets charged or fulfilled.
- Flag integrations that treat the browser or mobile client as a trusted source of payment-critical values.

- Check whether the primary controls for this topic are explicitly implemented and enforced at the right trust boundary.
- Check failure behavior, defaults, and alternate paths rather than only the intended flow.
- Flag places where documentation or assumptions replace visible technical enforcement.

### Callback Authentication and Payment Verification
- Check signature or HMAC verification on webhooks, callbacks, and asynchronous notifications.
- Check that payment status is verified with the gateway or trusted server-to-server channel before fulfillment.
- Flag flows that fulfill on redirect return alone or trust unsigned callback payloads.

- Check how untrusted input, third-party integrations, and cross-component boundaries affect this topic.
- Check whether surrounding systems can bypass or weaken the expected control model.
- Flag inconsistent enforcement across components, services, clients, or environments.

### Idempotency and Replay Resistance
- Check unique transaction identifiers, replay detection, duplicate callback handling, and one-time fulfillment logic.
- Check race conditions between multiple callback paths or between callback and manual verification paths.
- Flag systems where duplicate callbacks can create duplicate credits, shipments, or ledger changes.

- Check tests, logging, monitoring, and deployment practices relevant to this topic.
- Check whether operational handling preserves the intended security properties over time.
- Treat missing evidence about important controls as review gaps.

### Fraud Detection and Evidence Quality

- Check logging of payment attempts, callback verification failures, anomalous retries, and suspicious transaction patterns.
- Check whether fraud indicators and alerts are available for investigation and customer support response.
- Check whether evidence retained is sufficient to distinguish spoofing, replay, and integration failure.

## Output Contract

Structure every review response exactly in this order.

### 1. Findings

- Start with confirmed findings only.
- Order findings by severity, then by exploitability.
- If there are no confirmed findings, state `No confirmed findings for this review topic.`

Use this format for each finding:

```markdown
Title: <short vulnerability statement>
Severity: Critical | High | Medium | Low
Confidence: High | Medium | Low
Affected area: <flow, endpoint, component, or trust boundary>
Evidence: <specific file, behavior, response pattern, configuration, or code path>
Why it matters: <attacker outcome and impact>
Recommendation: <minimal concrete remediation>
OWASP basis: <relevant topic from this skill>
```

### 2. Review Gaps

```markdown
- Could not verify <control or flow> because <missing evidence>.
```

### 3. Passed Checks

```markdown
- <control> appears correctly implemented based on <evidence>.
```

### 4. Overall Assessment

- End with a short conclusion covering the dominant risk theme for this topic.

## Severity Guidance

- Critical: Attackers can tamper with payment-critical values or trigger fulfillment without verified successful payment.
- High: Callback authentication, replay protection, or server-side verification is weak enough to materially increase fraud success.
- Medium: Fraud telemetry, retry handling, or sequencing gaps increase risk but need additional conditions to cause loss.
- Low: Monitoring and evidence-retention improvements with stronger primary payment controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects order integrity, callback trust, idempotency, or fraud detection.
- Mention the exact callback, transaction field, or fulfillment step involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
