---
name: security-review-owasp-credential-stuffing-prevention
description: Review MFA coverage, rate limiting, device and IP intelligence, and abuse detection against OWASP credential stuffing prevention guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Credential Stuffing Prevention Security Review

Apply this skill to security reviews focused on Credential Stuffing Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on defenses against large-scale credential replay, password spraying, and distributed authentication abuse, especially where login flows trust static thresholds or lack adaptive controls. Prefer precise findings over broad advice.

## Scope

- login, MFA, password reset, session visibility, and authentication risk-scoring flows exposed to attackers
- rate limiting, proxy or IP handling, device and connection fingerprinting, and leaked-password checking
- telemetry, alerting, user notification, and operational playbooks for credential-stuffing detection and response

## Review Goals

- Find places where attackers can scale credential reuse attacks because the system lacks layered, adaptive authentication abuse defenses.
- Prioritize missing MFA for high-value accounts, ineffective rate limits, and absent detection of distributed attacks over minor UX issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify public authentication flows, privileged account types, and attack-rate control points in scope.
2. Trace how the system reacts to repeated failures across accounts, IPs, devices, and changing browser or network fingerprints.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable scalable account takeover attempts without strong friction or detection.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing layered throttling, MFA coverage, and abuse-detection telemetry end to end.

## Review Checklist

### MFA and Step-Up Coverage

- Check MFA requirements for administrative, high-risk, and sensitive-user populations.
- Check adaptive MFA triggers for new device, unusual location, hostile network reputation, or suspicious login cadence.
- Flag login or recovery paths that bypass stronger factors under predictable attacker conditions.

### Rate Limiting and Network Abuse Controls

- Check per-account, per-IP, per-device, and burst-aware rate limiting rather than relying on a single static threshold.
- Check handling for proxy networks, hosting providers, and distributed low-and-slow patterns.
- Flag controls that are easy to evade by rotating accounts, IPs, or user agents.

### Device Intelligence and Password Exposure Controls

- Check whether device or connection fingerprints are used to detect hostile automation or new-device risk.
- Check leaked-password screening at registration, reset, and password change.
- Flag login security models that depend solely on passwords plus basic throttling.

### Detection, Notification, and User Response

- Check user-visible session history, prior-login visibility, and notifications for suspicious access.
- Check alerting, metrics, and playbooks for stuffing spikes, MFA failure patterns, and unusual reset activity.
- Check whether mitigations are measurable and tuned using observed abuse rather than one-time defaults.

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

- Critical: Privileged or high-value accounts can be attacked at scale without MFA or meaningful throttling and detection.
- High: Layered defenses are missing or easy to evade, materially increasing credential-stuffing success.
- Medium: Detection, notifications, or leaked-password controls are incomplete but primary friction still exists.
- Low: Tuning and observability gaps with stronger primary anti-stuffing controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects MFA, throttling, device intelligence, or detection and user response.
- Mention the exact login flow, account class, or abuse pattern involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
