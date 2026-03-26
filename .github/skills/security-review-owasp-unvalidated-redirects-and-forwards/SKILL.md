---
name: security-review-owasp-unvalidated-redirects-and-forwards
description: Review Unvalidated Redirects and Forwards concerns, trust boundaries, and operational assumptions against OWASP Unvalidated Redirects and Forwards guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Unvalidated Redirects and Forwards Security Review

Apply this skill to security reviews focused on Unvalidated Redirects and Forwards concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on attacker-controlled input crossing into interpreters, selectors, binders, redirect targets, or privileged object mutation without strict control. Prefer precise findings over broad advice.

## Scope

- data ingestion, normalization, validation, binding, serialization, and execution paths related to this topic
- application boundaries where strings, objects, or selectors are interpreted by databases, shells, frameworks, or protocols
- library configuration, allowlists, and privilege boundaries that determine exploitability

## Review Goals

- Find weaknesses where untrusted input reaches interpreters, object mappers, redirectors, or privileged state mutation in unsafe form.
- Prioritize direct code execution, query manipulation, privilege mutation, or arbitrary target selection over lower-impact formatting issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the untrusted inputs, interpreters, binders, and high-trust outputs in scope.
2. Trace how untrusted data is transformed before it reaches execution, binding, or routing decisions.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable execution, data tampering, privilege changes, or attacker-controlled routing.

## Review Checklist

### Input-to-Interpreter Boundaries

- Check whether untrusted input reaches commands, queries, selectors, templates, deserializers, or redirect targets without strict control.
- Check allowlists, parameterization, canonicalization, and type constraints rather than relying on blacklists.
- Flag concatenation, reflective access, or dynamic dispatch patterns that expand attacker control.

### Binding and Mutation Safety

- Check object mapping, mass assignment, prototype inheritance, and property binding for unexpected field or structure injection.
- Check whether internal-only fields, security-sensitive attributes, or redirect targets can be set indirectly by user input.
- Flag validation that runs too late, after mutation or interpretation has already occurred.

### Privilege and Blast Radius

- Check runtime privileges, service accounts, and downstream capabilities to assess the impact of successful injection or unsafe binding.
- Check error handling, logging, and tests for signs of blocked or successful exploit attempts.
- Check for second-order injection paths where malicious input is stored first and interpreted later.

### Topic Focus Areas

- Review untrusted inputs, transformation steps, and source-to-sink paths most affected by this topic.
- Review allowlisting, parameterization, normalization, and structural validation controls relevant to this topic.
- Review dangerous interpreters, mappers, redirects, serializers, or command/query sinks that this topic is meant to constrain.
- Review downstream privilege boundaries and blast radius if this topic fails at runtime.
- Review tests, telemetry, and error handling for blocked, bypassed, or second-order abuse paths related to this topic.

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

- Critical: Input reaches privileged execution, arbitrary query or command control, or sensitive object mutation with high-impact outcomes.
- High: Strong exploit paths exist but require some environmental or contextual conditions.
- Medium: Important validation, allowlist, or privilege-boundary gaps that raise exposure without proving full compromise alone.
- Low: Hardening and observability issues with stronger primary protections otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects interpretation, binding, mutation, or routing.
- Mention the exact sink, mapper, command, query, or redirect behavior involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
