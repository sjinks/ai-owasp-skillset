---
name: security-review-owasp-insecure-direct-object-reference-prevention
description: Review object-level authorization, identifier handling, workflow state binding, and enumeration resistance against OWASP IDOR guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Insecure Direct Object Reference Prevention Security Review

Apply this skill to security reviews focused on Insecure Direct Object Reference Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on object lookups and modifications that trust client-supplied identifiers without verifying ownership or authorization. Prefer precise findings over broad advice.

## Scope
- REST, GraphQL, RPC, and server-rendered flows that fetch, mutate, export, or delete user-associated objects by identifier
- identifier generation, exposure, and multi-step workflow state handling that affect object access decisions
- object-level authorization in batch operations, nested resources, admin paths, and service-to-service object access

- code paths, configuration, infrastructure, tests, and workflows related to this topic
- trust boundaries, dependencies, integrations, and operational controls influenced by this topic
- documentation or security assumptions that materially affect how the controls are implemented

## Review Goals
- Find places where authentication exists but object-level authorization does not, or where identifiers are easy to tamper with or enumerate.
- Prioritize arbitrary object read or write access over identifier complexity issues that still retain strong authorization.

- Find weaknesses and missing controls that materially affect the security properties of this topic.
- Prioritize issues that are exploitable, exposed to untrusted input, or central to the trust boundaries in scope.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every flow where a client, service, or browser supplies an object identifier or reference.
2. Trace how that identifier is validated against the authenticated subject, workflow state, and permission model.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether attackers can access arbitrary objects with simple identifier manipulation.

## Review Checklist

### Object-Level Authorization
- Check that every object lookup or mutation enforces ownership or permission checks in the query or immediately afterward.
- Check batch and list operations for per-object authorization, not just top-level endpoint checks.
- Flag code that authorizes the route but not the object instance being accessed.

- Check whether the primary controls for this topic are explicitly implemented and enforced at the right trust boundary.
- Check failure behavior, defaults, and alternate paths rather than only the intended flow.
- Flag places where documentation or assumptions replace visible technical enforcement.

### Identifier Handling and Workflow Binding
- Check whether identifiers are exposed in URLs, bodies, forms, or client state and whether that exposure is necessary.
- Check multi-step flows for server-side binding of object identity instead of trusting hidden fields or client-returned IDs.
- Flag sequential or guessable identifiers when object-level authorization is weak or absent.

- Check how untrusted input, third-party integrations, and cross-component boundaries affect this topic.
- Check whether surrounding systems can bypass or weaken the expected control model.
- Flag inconsistent enforcement across components, services, clients, or environments.

### API and Administrative Edge Cases
- Check GraphQL resolvers, export endpoints, admin tooling, and background jobs for object-level auth gaps.
- Check service accounts and internal APIs for overly broad object access relative to their purpose.
- Flag overlooked non-UI paths that bypass the checks present in the main application flow.

- Check tests, logging, monitoring, and deployment practices relevant to this topic.
- Check whether operational handling preserves the intended security properties over time.
- Treat missing evidence about important controls as review gaps.

### Detection and Testing

- Check tests that attempt cross-user or cross-tenant access by changing identifiers.
- Check monitoring and logs for repeated unauthorized object access attempts.
- Check whether complexity of identifiers is being treated as a substitute for authorization.

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

- Critical: Attackers can read, modify, export, or delete arbitrary objects through identifier tampering.
- High: Object-level authorization is inconsistently applied, leaving important data sets or actions exposed.
- Medium: Identifier handling and workflow binding are weak but strong per-object authorization still limits abuse.
- Low: Enumeration resistance and logging improvements with stronger core object authorization otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects object authorization, identifier handling, workflow binding, or edge-path coverage.
- Mention the exact object type, endpoint, resolver, or identifier field involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
