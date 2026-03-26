---
name: security-review-owasp-authorization-patterns
description: Review Authorization Patterns concerns, trust boundaries, and operational assumptions against OWASP Authorization Patterns guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Authorization Patterns Security Review

Apply this skill to security reviews focused on Authorization Patterns concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on account takeover, identity confusion, privilege escalation, recovery bypass, weak challenge flows, and broken trust propagation. Prefer precise findings over broad advice.

## Scope

- identity, credential, enrollment, recovery, step-up, and session-adjacent flows related to this topic
- user-to-service and service-to-service trust boundaries, propagated identity context, and privilege decisions
- tests, operational controls, and documentation that define how identity and authorization assumptions are enforced

## Review Goals

- Find weaknesses that let attackers assume another identity, bypass account controls, or exercise privileges they should not have.
- Prioritize direct account-takeover and privilege-boundary failures over lower-impact identity hygiene issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the user journeys, trust boundaries, and privilege changes in scope.
2. Trace identity proof, credential handling, propagation, and authorization decisions end to end.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable account takeover, privilege escalation, or silent identity confusion.

## Review Checklist

### Identity Proof and Recovery

- Check enrollment, recovery, and reset flows for strong identity proof appropriate to the risk.
- Flag fallback paths that are weaker than the primary control set and can bypass expected protections.
- Check for user enumeration, weak secrets, or weak challenge factors in account recovery or profile-change flows.

### Privilege and Trust Propagation

- Check how identity and authorization context moves across services, background jobs, and delegated actions.
- Flag privilege decisions based on client-controlled claims, headers, or unauthenticated context.
- Check whether step-up or transaction-specific authorization is required where risk increases.

### Assurance, Logging, and Failure Handling

- Check that failures are safe, auditable, and rate-limited where attacker iteration is possible.
- Check that important identity changes generate notifications and reviewable audit trails.
- Check tests and monitoring for bypasses across alternate login and service entry points.

### Topic Focus Areas

- Review the account, credential, recovery, and privilege-boundary workflows most affected by this topic.
- Review abuse cases where this topic can be bypassed, downgraded, or used to improve attacker account access.
- Review how this topic is enforced across primary, fallback, administrative, and self-service identity flows.
- Review logging, notifications, throttling, and review evidence that support detection and response for this topic.
- Review whether this topic is consistently tested and enforced across account lifecycle changes.

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

- Critical: Weaknesses that enable direct account takeover, identity impersonation, or broad privilege escalation.
- High: Reliable recovery bypasses, unsafe trust propagation, or missing high-risk reauthentication controls.
- Medium: Important rate-limiting, auditing, or step-up gaps that require additional conditions to exploit.
- Low: Hardening and observability issues with stronger identity controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects identity proof, recovery, propagation, or privilege enforcement.
- Mention whether the likely impact is account takeover, privilege escalation, or trust-boundary confusion.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
