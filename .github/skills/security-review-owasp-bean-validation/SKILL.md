---
name: security-review-owasp-bean-validation
description: Review Jakarta Bean Validation constraint coverage, trigger points, regex safety, and custom validators against OWASP Bean Validation guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Bean Validation Security Review

Apply this skill to security reviews focused on Bean Validation concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on missing or ineffective validation constraints, unsafe regex patterns, missing `@Valid` triggers, and custom validators that fail open or perform unsafe logic. Prefer precise findings over broad advice.

## Scope

- Java request models, DTOs, entities, method parameters, return values, and nested beans validated with Jakarta or Hibernate Validator
- validation trigger points at controllers, REST endpoints, service boundaries, and object graph traversal
- regex-backed constraints, custom constraint annotations, and validator implementations that affect security and abuse resistance

## Review Goals

- Find places where bean validation is missing, inconsistently applied, or too weak to enforce security-relevant input expectations.
- Prioritize missing validation on public inputs, unsafe regexes, and broken custom validators over cosmetic constraint quality issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify externally controlled inputs and the beans, DTOs, and method arguments that receive them.
2. Trace where validation is declared, where it is triggered, and where unvalidated objects can still reach business logic.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether invalid or malicious data can cross a trust boundary into security-sensitive logic.

## Review Checklist

### Constraint Coverage

- Check that externally controlled fields have appropriate constraints for size, format, ranges, and allowed structure.
- Check that sensitive fields such as passwords, tokens, identifiers, and URLs have explicit constraints instead of relying on client behavior.
- Flag models where validation exists only for some entry points while equivalent inputs bypass it elsewhere.

### Validation Trigger Points

- Check `@Valid` or equivalent triggering on public request handlers, nested beans, and method validation paths.
- Check whether nested object graphs require cascading validation and whether it is actually enabled.
- Flag code paths where objects are deserialized or mapped but never validated before use.

### Regex and Custom Validator Safety

- Check `@Pattern` expressions for catastrophic backtracking, blacklist-heavy logic, and overly permissive matching.
- Check custom validators for deterministic fail-closed behavior and absence of unsafe side effects.
- Flag validators that mix business actions, reflection, or dangerous parsing into what should be pure validation.

### Error Handling and Test Coverage

- Check whether validation failures are surfaced safely without leaking internal model details.
- Check tests for invalid payloads, nested validation, and regex edge cases rather than only valid inputs.
- Check whether validation exceptions are consistently handled across controllers and background entry points.

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

- Critical: Public inputs bypass validation entirely or validator logic enables ReDoS or equivalent high-impact abuse.
- High: Important fields or nested objects are unvalidated, or custom validators materially weaken trust-boundary enforcement.
- Medium: Constraints are present but permissive, inconsistently triggered, or insufficiently tested.
- Low: Error-message and consistency gaps with stronger primary validation controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects constraint coverage, trigger points, regex safety, or validator implementation.
- Mention the exact annotation, bean, method boundary, or validator involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
