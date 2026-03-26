---
name: security-review-owasp-input-validation
description: Review server-side validation logic, allowlists, regex safety, semantic checks, and high-risk input types against OWASP input validation guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Input Validation Security Review

Apply this skill to security reviews focused on Input Validation concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on missing server-side validation, over-reliance on denylists, unsafe regexes, type confusion, missing semantic checks, and dangerous handling of email, file upload, and rich content inputs. Prefer precise findings over broad advice.

## Scope

- server-side validation of web, API, partner, and backend-sourced input
- syntactic validation, semantic validation, type conversion, and business-rule checks
- allowlist and denylist strategy, regex safety, and normalization
- validation of high-risk inputs such as email addresses, uploaded files, and rich content
- interaction between validation, output encoding, and content sanitization

## Review Goals

- Find paths where untrusted input reaches business logic, storage, or security-sensitive code without authoritative server-side validation.
- Prioritize missing server-side validation, unsafe allowlist design, and dangerous input classes over purely UX-focused validation gaps.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every ingress path for untrusted data, including clients, APIs, background feeds, and imported content.
2. Trace the data through parsing, validation, normalization, coercion, business logic, storage, and rendering.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether the invalid input can influence control flow, persistence, or a downstream exploit sink.

## Review Checklist

### Core Validation Strategy

- Check that authoritative validation happens server-side for every untrusted input source.
- Check that validation happens before sensitive processing rather than after the input is already used.
- Prefer allowlists that define what is valid. Treat denylists as supplemental only.
- Check that both syntactic correctness and semantic or business-rule correctness are enforced.

### Type, Structure, and Regex Safety

- Check that type coercion is explicit and rejects invalid values rather than silently converting them.
- Check min and max length, range, and format constraints for structured input.
- Flag regexes that are unanchored, overly permissive, or vulnerable to catastrophic backtracking.
- Check whether free-form Unicode input is normalized and validated with a deliberate character policy.

### Fixed Sets and Semantic Validation

- Check that enum-like inputs, status values, roles, or other fixed-set fields are validated against canonical server-side values.
- Check semantic rules such as date ordering, numeric bounds, ownership, state transitions, or other business invariants.
- Flag trust in client-provided dropdown values or hidden fields without server-side verification.

### High-Risk Input Types

- Check email validation for both syntax sanity and proof of ownership where the business flow requires it.
- Check file-upload validation for filename handling, size limits, extension allowlists, archive handling, and dangerous file types.
- Check rich content flows to ensure validation is paired with proper sanitization rather than treated as a substitute.

### Validation Boundaries and Output Context

- Check that client-side validation is treated as UX only, not as the security boundary.
- Check that input validation is not misrepresented as the primary XSS defense where output encoding or sanitization is required.
- Check whether invalid-input failures are handled consistently without letting partially validated data continue downstream.

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

- Critical: Missing server-side validation or validation-after-use patterns that let attacker input directly reach sensitive operations.
- High: Broken allowlists, unsafe regexes, missing validation for fixed-set or high-risk inputs, or dangerous file and email validation gaps.
- Medium: Important semantic-validation, normalization, or coercion gaps that increase exploitability but do not independently prove compromise.
- Low: UX-oriented or defense-in-depth gaps where strong server-side validation is otherwise clearly present.

## Findings Format Rules

- State whether the weakness is syntactic, semantic, type-related, regex-related, or tied to a special input class.
- Mention the ingress path and the downstream sink or logic affected.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
