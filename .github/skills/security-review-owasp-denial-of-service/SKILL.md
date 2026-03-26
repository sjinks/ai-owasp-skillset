---
name: security-review-owasp-denial-of-service
description: Review resource exhaustion guards, rate limiting, timeout policy, and graceful degradation against OWASP denial of service guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Denial of Service Security Review

Apply this skill to security reviews focused on Denial of Service concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on application-layer resource exhaustion, unbounded work, missing rate controls, brittle session or connection handling, and failure modes that cascade into broad unavailability. Prefer precise findings over broad advice.

## Scope
- request parsing, file handling, loops, expensive computations, session storage, connection handling, and concurrency limits
- timeouts, bandwidth and request-rate controls, backpressure, queue depth, and thread or worker saturation behavior
- resilience patterns such as graceful degradation, redundancy, and avoidance of single points of failure

- code paths, configuration, infrastructure, tests, and workflows related to this topic
- trust boundaries, dependencies, integrations, and operational controls influenced by this topic
- documentation or security assumptions that materially affect how the controls are implemented

## Review Goals
- Find ways attackers can trigger disproportionate CPU, memory, storage, session, or connection consumption.
- Prioritize unbounded resource use, missing timeouts, and lack of graceful degradation over secondary observability issues.

- Find weaknesses and missing controls that materially affect the security properties of this topic.
- Prioritize issues that are exploitable, exposed to untrusted input, or central to the trust boundaries in scope.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify attacker-controlled inputs and request patterns that drive expensive work or durable resource consumption.
2. Trace which guards, timeouts, concurrency limits, and failover mechanisms bound that work.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they can deny service broadly with modest attacker effort.

## Review Checklist

### Resource Exhaustion Prevention
- Check request, file, payload, query, and object-size limits before expensive processing begins.
- Check loops, memory allocation, session storage, and synchronous work triggered by attacker input for explicit bounds.
- Flag code paths where low-cost input can cause high-cost computation or storage growth.

- Check whether the primary controls for this topic are explicitly implemented and enforced at the right trust boundary.
- Check failure behavior, defaults, and alternate paths rather than only the intended flow.
- Flag places where documentation or assumptions replace visible technical enforcement.

### Rate Limiting and Connection Controls
- Check per-IP, per-user, or per-endpoint rate limits and concurrency ceilings for attack-relevant paths.
- Check connection, read, write, and idle timeout settings, including protection against slow-client abuse.
- Flag surfaces that trust the network or proxy tier to absorb abuse without application-level controls.

- Check how untrusted input, third-party integrations, and cross-component boundaries affect this topic.
- Check whether surrounding systems can bypass or weaken the expected control model.
- Flag inconsistent enforcement across components, services, clients, or environments.

### Resilience and Graceful Degradation
- Check for single points of failure, blocking dependencies, and lack of bulkheads or load shedding in critical paths.
- Check whether the system degrades safely under load instead of failing all-or-nothing.
- Flag exception handling and retry behavior that amplify traffic or keep saturated resources locked.

- Check tests, logging, monitoring, and deployment practices relevant to this topic.
- Check whether operational handling preserves the intended security properties over time.
- Treat missing evidence about important controls as review gaps.

### Authentication and Early Rejection

- Check that authentication and validation occur before expensive downstream work where possible.
- Check that lockout, CAPTCHA, and abuse defenses do not themselves create denial-of-service conditions against users.
- Check tests and metrics for saturation, throttling, and graceful-failure scenarios rather than only normal traffic.

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

- Critical: Attackers can exhaust core resources or trigger a system-wide outage with little effort because key bounds are absent.
- High: Important timeouts, rate limits, or graceful-degradation controls are missing or ineffective.
- Medium: Saturation and resilience issues increase outage risk but need stronger traffic volume or supporting conditions.
- Low: Tuning and observability gaps with stronger primary DoS controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects resource bounds, rate controls, resilience, or early rejection.
- Mention the exact expensive operation, timeout, or saturation point involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
