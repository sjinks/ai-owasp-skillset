---
name: security-review-owasp-secure-code-review
description: Review trust boundaries, authorization paths, input handling, secrets, and business logic defects against OWASP secure code review guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Secure Code Review

Apply this skill to security reviews focused on Secure Code Review concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on code paths where attacker input, authentication state, authorization decisions, secrets, or business rules can be subverted even if automated tooling is present. Prefer precise findings over broad advice.

## Scope

- source-level trust boundaries for input validation, output handling, state transitions, and privilege checks
- authentication, session, authorization, cryptographic, secret-handling, and workflow-sensitive code paths
- manual review coverage that should complement rather than repeat static or dynamic tooling output

## Review Goals

- Find code-level weaknesses that allow exploitation through input handling, business logic, session state, missing authorization checks, or unsafe crypto and secret usage.
- Prioritize direct compromise paths and business-rule bypasses over style, maintainability, or generic process observations.
- Produce findings with concrete evidence from code, playbooks, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the highest-risk code paths, trust boundaries, privileged actions, and attacker-controlled inputs in scope.
2. Trace how those paths validate input, enforce identity and authorization, manage secrets, and preserve workflow invariants.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by exploitability, impact on sensitive operations, and whether they bypass a core trust boundary.

## Review Checklist

### Input Handling and Dangerous Sinks

- Check that server-side validation exists for all attacker-controlled input regardless of client-side checks.
- Check that output encoding and sink selection match the rendering or execution context.
- Flag query, command, template, file, or parser interactions that consume untrusted input without strict control.

### Authentication, Session, and Authorization

- Check password handling, reset flows, MFA gates, session lifecycle, and reauthentication for sensitive actions.
- Check server-side authorization on every sensitive resource and transition, including ID-based lookups and post-auth checks.
- Flag business flows where state changes or privilege changes can be triggered out of sequence or without proper identity context.

### Secrets, Crypto, and Business Logic

- Check that secrets are not hardcoded, weakly stored, or logged, and that cryptographic choices are modern and correctly enforced.
- Check business rules for race conditions, replay, inconsistent state transitions, or direct API paths that bypass intended controls.
- Flag code that technically validates data but still permits unsafe outcomes because workflow rules are missing.

### Review Coverage and Evidence Quality

- Check that manual review coverage includes the parts automated scanners commonly miss: business logic, authorization context, and workflow abuse.
- Check that findings and passed checks are backed by actual code evidence rather than assumptions from framework defaults.
- Check that tests or review notes cover negative and abuse-case paths rather than only the intended behavior.

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

- Critical: Missing or broken checks enable direct unauthorized access, injection, secret compromise, or high-impact business logic abuse.
- High: Important trust-boundary or authorization flaws materially increase attacker success or compromise sensitive workflows.
- Medium: Validation, session, crypto, or workflow hardening gaps increase risk but usually need additional conditions.
- Low: Review-coverage or defense-in-depth issues with stronger primary protections otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects input handling, identity, authorization, secret handling, crypto, or business logic.
- Mention the exact code path, trust boundary, or workflow step involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
