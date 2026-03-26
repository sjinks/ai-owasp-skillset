---
name: security-review-owasp-mass-assignment
description: Review automatic binding, DTO boundaries, sensitive field exposure, and framework allowlisting against OWASP mass assignment guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Mass Assignment Security Review

Apply this skill to security reviews focused on Mass Assignment concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on automatic request binding that lets attackers set privileged or hidden fields through framework or ORM conveniences. Prefer precise findings over broad advice.

## Scope

- framework model binding, form deserialization, JSON-to-object mapping, and ORM or ODM assignment paths reachable from user input
- DTOs, view models, and explicit mapping layers that separate public input from privileged domain fields
- framework-specific allowlists or deny-by-default controls around sensitive attributes such as role, status, and financial values

## Review Goals

- Find places where attackers can set fields they were never meant to control, especially authorization, billing, or account-state fields.
- Prioritize direct privilege escalation and integrity-impacting field injection over low-value accidental field binding.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify endpoints and forms that bind request data directly into models, entities, or nested objects.
2. Trace which fields are bindable, which are intended to remain internal, and where explicit mapping is or is not used.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they permit privilege changes, account-state tampering, or financial and identity-field manipulation.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for tracing bindable fields across controllers, DTOs, and persistence models.

## Review Checklist

### Binding Boundaries and DTO Use

- Check whether public request models are distinct from persistence or domain models.
- Check whether explicit property mapping is used instead of blind bulk assignment.
- Flag direct binding from request bodies or forms into ORM or domain entities.

### Sensitive Field Protection

- Check that fields such as `isAdmin`, `role`, `permissions`, `accountStatus`, `price`, `discount`, `ownerId`, and similar sensitive attributes are not bindable from user input.
- Check nested object binding for privilege-bearing subobjects or references.
- Flag blocklist-only approaches where a stable allowlist is practical and safer.

### Framework Controls and Test Coverage

- Check framework-specific binding controls, such as allowed fields, fillable attributes, ignored properties, or binder customization.
- Check negative tests that attempt to set privileged or hidden fields through the public interface.
- Flag codebases that assume constructors or UI omissions are enough to prevent unwanted binding.

### Downstream Integrity Impact

- Check whether injected fields can alter ownership, approval state, pricing, account recovery targets, or other high-impact business data.
- Check whether internal-only fields are rehydrated later from previously bound user data.
- Flag flows where mass assignment can silently persist across later steps without immediate visibility.

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

- Critical: Mass assignment permits direct privilege escalation, ownership takeover, or similarly high-impact account or business-state manipulation.
- High: Sensitive but non-admin fields such as pricing, status, or identity attributes can be attacker-controlled.
- Medium: Allowlist and DTO boundaries are weak but the bound fields are currently low impact.
- Low: Hardening and test-coverage gaps with stronger primary binding controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects binding boundaries, sensitive fields, framework controls, or downstream integrity.
- Mention the exact bindable field, DTO gap, or framework binding path involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
