---
name: security-review-owasp-prototype-pollution-prevention
description: Review object creation, deep merge behavior, prototype mutation paths, and Node.js hardening against OWASP prototype pollution guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Prototype Pollution Prevention Security Review

Apply this skill to security reviews focused on Prototype Pollution Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on untrusted data that can write into `__proto__`, `constructor.prototype`, or equivalent object inheritance paths, including library helpers and framework binding code. Prefer precise findings over broad advice.

## Scope

- JavaScript and Node.js object creation, merge, clone, parse, and property-assignment paths that consume untrusted data
- custom deep-merge utilities, query-string parsers, JSON handling, and third-party helpers that can mutate shared prototypes
- runtime mitigations such as null-prototype objects, `Map` or `Set`, frozen objects, and Node.js prototype hardening options

## Review Goals

- Find code and library paths where attacker-controlled properties can poison inherited behavior or security-sensitive defaults.
- Prioritize exploitation paths that lead to RCE, auth bypass, privilege mutation, or broad logic corruption over low-impact object-shape issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every path where untrusted data becomes an object, nested structure, or merge operand.
2. Trace whether that path can write inherited properties or influence shared prototypes before later trust decisions.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether polluted properties can alter execution, authorization, or configuration at scale.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for tracing object construction and merge behavior through the codebase.

## Review Checklist

### Object Creation and Safe Data Structures

- Check whether untrusted dictionaries use `Map`, `Set`, or `Object.create(null)` rather than normal objects when practical.
- Check whether security-sensitive config objects are frozen, sealed, or otherwise shielded from inherited property surprises.
- Flag object construction patterns that assume inherited properties are harmless or absent.

### Prototype Mutation Attack Surface

- Check for writes to `__proto__`, `constructor`, `constructor.prototype`, or equivalent mutation paths in direct code or helper utilities.
- Check deep merge, clone, and assignment utilities for inherited-property writes from untrusted input.
- Flag spread, `Object.assign`, or recursive merge patterns that accept attacker-controlled nested structures without screening dangerous keys.

### Library and Runtime Defenses

- Check third-party query parsing, config merging, and serialization libraries for known prototype pollution risks and hardened configuration.
- Check Node.js runtime mitigations such as `--disable-proto=delete` where they are practical and documented.
- Check tests for prototype-pollution payloads rather than only ordinary field-validation cases.

### Impact and Second-Order Abuse

- Check whether polluted properties could influence authorization flags, templating behavior, request routing, or code execution later in the request lifecycle.
- Check whether attacker-controlled polluted data can persist and affect later requests or other tenants.
- Flag cases where the exploit surface is indirect but the blast radius is application-wide.

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

- Critical: Prototype pollution can change execution flow, authorization, or runtime behavior in ways that enable RCE or broad compromise.
- High: Dangerous prototype writes are possible and can materially alter security-sensitive application behavior.
- Medium: Safer data-structure and library controls are missing but exploitation still depends on additional code patterns.
- Low: Defense-in-depth and runtime-hardening gaps with stronger object-handling controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects object creation, prototype mutation, library behavior, or second-order impact.
- Mention the exact dangerous key path, utility, or polluted property involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
