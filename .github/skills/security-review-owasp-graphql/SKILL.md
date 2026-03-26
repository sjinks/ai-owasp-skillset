---
name: security-review-owasp-graphql
description: Review GraphQL schema exposure, resolver authorization, query resource limits, and downstream injection risk against OWASP GraphQL guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP GraphQL Security Review

Apply this skill to security reviews focused on GraphQL concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on resolver-level authorization gaps, introspection exposure, expensive query abuse, batching attacks, and injection risks in downstream systems reached through GraphQL. Prefer precise findings over broad advice.

## Scope

- GraphQL schemas, queries, mutations, subscriptions, resolver code, schema tooling, and production configuration
- authorization checks on nodes, edges, IDs, unions, interfaces, and mutations rather than only at the top-level route
- query depth, cost, batching, pagination, introspection, error handling, and downstream API or database interactions

## Review Goals

- Find GraphQL-specific weaknesses that expose private data, enable injection, or permit resource exhaustion through query shape rather than traditional endpoint count.
- Prioritize authorization gaps, introspection leakage, batching abuse, and resolver-driven injection over generic platform hardening issues.
- Produce findings with concrete evidence from code, configuration, manifests, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the schema, resolver layers, data sources, auth boundaries, and production configuration in scope.
2. Trace how user-controlled query structure and arguments affect data access, downstream calls, and execution cost.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they expose private data, bypass resolver authorization, or enable substantial resource exhaustion.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for tracing resolver behavior, query cost controls, and production schema exposure.

## Review Checklist

### Authorization and Data Exposure

- Check authorization at edges and nodes, not only at entry resolvers or route middleware.
- Check ID-based lookup fields, search connections, unions, and interfaces for object-level access control.
- Flag schemas that expose sensitive types or fields broadly even when the top-level query is authenticated.

### Query Cost, Batching, and DoS Resistance

- Check query depth, complexity, pagination, and list-size limits.
- Check batching, persisted queries, rate limiting, and timeouts for brute-force and enumeration abuse.
- Flag resolver chains that can be amplified into N+1 or other expensive execution patterns with attacker-controlled query shapes.

### Downstream Injection and Error Handling

- Check whether resolver arguments are validated and parameterized before reaching SQL, NoSQL, LDAP, HTTP, shell, XML, or template sinks.
- Check introspection, GraphiQL, debug stacks, and error messages in production.
- Flag field suggestions, schema hints, or verbose errors that leak useful attack information.

### Schema and Operational Controls

- Check whether introspection is disabled or tightly gated in production.
- Check whether schema evolution, auth tests, and abuse-case tests cover field-level and batching risks.
- Check whether operational monitoring can distinguish malicious query shapes from ordinary client mistakes.

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

- Critical: GraphQL flaws expose sensitive data broadly, bypass authorization, or enable near-unbounded resource exhaustion through normal client access.
- High: Resolver injection, introspection leakage, or batching and query-cost gaps materially improve attacker success.
- Medium: Important pagination, error-handling, or monitoring weaknesses increase exposure but need additional conditions.
- Low: Hardening and observability issues with stronger core GraphQL controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects authorization, query cost, downstream sinks, or schema exposure.
- Mention the exact field, resolver, query shape, or production setting involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
