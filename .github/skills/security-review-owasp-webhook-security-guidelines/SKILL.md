---
name: security-review-owasp-webhook-security-guidelines
description: Review Webhook Security Guidelines concerns, trust boundaries, and operational assumptions against OWASP Webhook Security Guidelines guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Webhook Security Guidelines Security Review

Apply this skill to security reviews focused on Webhook Security Guidelines concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on broken trust establishment, weak cryptographic configuration, insecure transport behavior, integrity failures, and misapplied client or service trust assumptions. Prefer precise findings over broad advice.

## Scope

- cryptographic configuration, transport setup, certificate or header-based trust, and message integrity controls related to this topic
- client-server and service-to-service channels, termination points, proxies, and downstream trust boundaries
- operational lifecycle, keying assumptions, and validation behavior that determine whether protection actually holds

## Review Goals

- Find weaknesses that let attackers observe, tamper with, replay, or spoof protected communications or protected data.
- Prioritize broken trust validation, weak algorithms or modes, missing integrity checks, and unsafe termination assumptions over lower-impact operational issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the protected channel, cryptographic material, trust anchors, and enforcement points in scope.
2. Trace how trust is established, validated, refreshed, and enforced across the relevant data flow.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable tampering, interception, spoofing, or broad trust bypass.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing signature verification, replay defense, endpoint trust, and delivery retries end to end.

## Review Checklist

### Trust Establishment and Validation

- Check certificate, header, token, or endpoint trust decisions for strict validation instead of implicit trust.
- Check hostname, audience, origin, signature, or peer validation wherever the topic depends on a trusted endpoint or message source.
- Flag trust-on-first-use, disabled verification, or broad trust stores that weaken authenticity guarantees.

### Confidentiality, Integrity, and Replay

- Check encryption, integrity, and replay protections appropriate to the sensitivity of the data or operation.
- Check downgrade, stripping, proxy, and termination behaviors that can silently weaken transport guarantees.
- Flag secrets, cookies, webhooks, or headers exposed over weak channels or without integrity verification.

### Lifecycle and Operational Hardening

- Check rotation, renewal, revocation, expiration, and failure behavior for certificates, secrets, or transport settings.
- Check monitoring and logging for trust failures, tampering, or unexpected channel behavior.
- Check tests and deployment automation for configuration drift across environments.

### Topic Focus Areas

- Review trust anchors, peer validation, and channel establishment mechanisms relevant to this topic.
- Review confidentiality, integrity, authenticity, and replay protections that depend on this topic.
- Review intermediary behavior such as proxies, gateways, TLS termination, and header forwarding that could weaken this topic.
- Review key, certificate, secret, or configuration lifecycle handling for this topic, including rotation and revocation.
- Review test coverage and monitoring for failures in trust validation, integrity checks, or transport policy enforcement.

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

- Critical: Trust-validation failures or weak transport settings that permit broad interception, tampering, or spoofing.
- High: Missing integrity or replay protections, unsafe termination assumptions, or weak cryptographic configuration with realistic abuse paths.
- Medium: Important lifecycle or deployment gaps that weaken protection but need additional conditions.
- Low: Hardening and operational visibility issues with stronger primary controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects authenticity, confidentiality, integrity, replay resistance, or lifecycle handling.
- Mention the trust anchor, header, certificate, or channel setting involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
