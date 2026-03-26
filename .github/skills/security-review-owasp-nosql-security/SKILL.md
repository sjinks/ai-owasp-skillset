---
name: security-review-owasp-nosql-security
description: Review NoSQL query safety, authentication and network exposure, secret handling, and operational hardening against OWASP NoSQL guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP NoSQL Security Review

Apply this skill to security reviews focused on NoSQL Security concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on NoSQL injection, unsafe operator exposure, weak database authentication, public network exposure, and insecure secret or backup handling. Prefer precise findings over broad advice.

## Scope
- NoSQL query construction, driver use, ODM patterns, and operator handling for MongoDB, DynamoDB, Cassandra, and similar systems
- authentication, RBAC, TLS, network reachability, and administrative exposure for NoSQL data stores
- database credentials, backups, logs, and dangerous server-side features such as scripting or raw query execution

- code paths, configuration, infrastructure, tests, and workflows related to this topic
- trust boundaries, dependencies, integrations, and operational controls influenced by this topic
- documentation or security assumptions that materially affect how the controls are implemented

## Review Goals
- Find weaknesses that let attackers manipulate NoSQL queries, reach databases directly, or abuse weak admin and credential controls.
- Prioritize NoSQL injection, unauthenticated exposure, and public reachability over lower-impact monitoring gaps.

- Find weaknesses and missing controls that materially affect the security properties of this topic.
- Prioritize issues that are exploitable, exposed to untrusted input, or central to the trust boundaries in scope.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify user-controlled inputs that influence NoSQL queries and the databases, ports, and admin paths in scope.
2. Trace how queries are built, how access is authenticated and authorized, and how secrets and backups are protected.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they expose direct data access or allow attacker-controlled query behavior.

## Review Checklist

### Query Safety and Injection Prevention
- Check that queries are built with safe driver objects instead of string concatenation, raw eval-like constructs, or direct operator pass-through.
- Check whether dangerous operators such as `$where`, `$regex`, `$expr`, or equivalent are reachable from untrusted input.
- Flag applications that claim ODM safety while still exposing raw query fragments or unvalidated operator keys.

- Check whether the primary controls for this topic are explicitly implemented and enforced at the right trust boundary.
- Check failure behavior, defaults, and alternate paths rather than only the intended flow.
- Flag places where documentation or assumptions replace visible technical enforcement.

### Authentication, Authorization, and Network Exposure
- Check database authentication, RBAC, separate application and admin accounts, and least privilege for data operations.
- Check listener binding, internal-only exposure, TLS use, and network segmentation around NoSQL services.
- Flag databases or admin consoles reachable from broad internal or public networks.

- Check how untrusted input, third-party integrations, and cross-component boundaries affect this topic.
- Check whether surrounding systems can bypass or weaken the expected control model.
- Flag inconsistent enforcement across components, services, clients, or environments.

### Secrets, Backups, and Dangerous Features
- Check credentials in code, images, CI output, env logging, and secret managers.
- Check backup protection, encryption, and access control for data and snapshots.
- Flag dangerous features such as server-side scripting, demo users, or insecure defaults that remain enabled.

- Check tests, logging, monitoring, and deployment practices relevant to this topic.
- Check whether operational handling preserves the intended security properties over time.
- Treat missing evidence about important controls as review gaps.

### Monitoring and Runtime Hardening

- Check audit logging and monitoring for admin actions, query spikes, and dangerous operator usage.
- Check whether driver and server versions align with expected security defaults.
- Check tests for operator injection, auth failures, and network-exposure assumptions.

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

- Critical: NoSQL injection or unauthenticated or publicly exposed database access is possible.
- High: Operator handling, authz, TLS, or secret management materially improve attacker access or impact.
- Medium: Hardening, backup, or monitoring gaps increase exposure but need additional attacker conditions.
- Low: Tuning and observability issues with stronger primary NoSQL protections otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects queries, auth and network exposure, secrets, or runtime hardening.
- Mention the exact operator, driver path, database listener, or secret boundary involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
