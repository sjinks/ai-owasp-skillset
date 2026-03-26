---
name: security-review-owasp-authorization
description: Review authorization-related code, policies, and configuration against OWASP authorization guidance. Use for access control design, object-level access checks, privilege boundaries, and authorization failures.
argument-hint: Optionally specify the access control model, endpoints, or files to focus on
---

# OWASP Authorization Security Review

Apply this skill to security reviews focused on Authorization concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on privilege escalation, missing access checks, broken tenant isolation, IDOR-style weaknesses, and policy enforcement gaps. Prefer precise findings over broad advice.

## Scope

Apply this skill to:

- access control model design such as RBAC, ABAC, and ReBAC
- route, handler, controller, middleware, and service-level authorization checks
- object and record access by identifier
- multi-tenant isolation and cross-tenant access boundaries
- static resource protection and storage access controls
- authorization failure handling and audit logging
- framework authorization configuration and custom policy code
- automated authorization testing and boundary-condition coverage

## Review Goals

- Find authorization bypasses and privilege escalation paths.
- Prioritize exploitable weaknesses such as horizontal access control failures, vertical privilege escalation, and deny-by-default gaps.
- Produce findings with concrete evidence from code, configuration, tests, or observable behavior.

## Review Procedure

1. Identify the protected resources and trust boundaries.
   Include endpoints, background jobs, admin functions, tenant-scoped data, and static resources.
2. Trace how authorization decisions are made.
   Check middleware, route guards, policy engines, service-layer checks, database scoping, and storage access rules.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by exploitability and data sensitivity.
   Multi-tenant isolation failures and broad privilege bypasses should rank above narrow policy hygiene issues.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for tracing access decisions through routes, services, queries, and storage layers.

## Review Checklist

### Architecture and Access Model

- Check whether the chosen access control model matches the application complexity and trust boundaries.
- Flag role designs that create broad or difficult-to-audit privilege grants.
- Check whether the system defaults to deny when no policy matches.
- Check whether the permission model is centralized enough to avoid scattered, inconsistent enforcement.

### Permission Enforcement

- Verify every protected endpoint or operation has an explicit server-side authorization check.
- Check whether authorization is enforced in middleware or shared policy code rather than inconsistently in individual handlers.
- Flag implicit allow behavior, inverted logic, or fallback paths that bypass checks.
- Check whether alternate routes, background workers, or internal APIs can bypass the main enforcement path.

### Object-Level and Tenant-Level Access

- Check whether access is validated for the specific resource being requested, not just the resource type.
- Flag direct object references controlled by the user without corresponding ownership or tenant checks.
- Check whether database queries are scoped to the authenticated principal or tenant where appropriate.
- Check whether predictable identifiers materially increase the risk of unauthorized access.
- Treat cross-tenant access failures as high-severity by default.

### Client and Service Boundaries

- Flag any authorization decision enforced only in the client.
- Check whether downstream services re-validate authorization assumptions instead of trusting unbounded upstream claims.
- Check whether tokens or sessions identify the subject while policy decisions are still evaluated server-side.

### Static Resources and Storage

- Check whether files, objects, and other static resources are protected by the same authorization rules as primary application data.
- Check cloud storage or object-store configuration for unintended public access.
- Check whether resource URLs leak access without an additional authorization gate.

### Error Handling and Observability

- Check whether access-denied responses avoid leaking resource existence or policy details beyond what is necessary.
- Check whether failed authorization attempts are logged with enough context for investigation.
- Check whether admin actions and sensitive access are audited.

### Testing and Framework Use

- Check whether tests cover role boundaries, tenant boundaries, and deny-by-default behavior.
- Check whether the framework authorization features are explicitly configured rather than assumed secure by default.
- Flag custom authorization logic that duplicates framework behavior without a clear need.

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

- List only the important places where evidence is missing and a conclusion cannot be reached.

Use this format:

```markdown
- Could not verify <control or flow> because <missing evidence>.
```

### 3. Passed Checks

- Summarize only notable controls that were clearly implemented correctly.

Use this format:

```markdown
- <control> appears correctly implemented based on <evidence>.
```

### 4. Overall Assessment

- End with a short conclusion covering the dominant authorization risk theme.

## Severity Guidance

- Critical: Cross-tenant data access, admin-level privilege bypass, or broad authorization failure with direct access to sensitive resources.
- High: Realistic horizontal privilege escalation, object-level access bypass, or missing checks on sensitive operations.
- Medium: Important authorization gaps that require additional conditions or have a narrower blast radius.
- Low: Hardening gaps, audit weaknesses, or narrowly scoped policy issues with limited exploitability.

## Findings Format Rules

- Do not speculate about hidden protections.
- Tie each recommendation to the specific access control weakness you observed.
- Group repeated missing checks into one finding when they share the same root cause.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- Do not produce a long narrative summary before listing findings.
