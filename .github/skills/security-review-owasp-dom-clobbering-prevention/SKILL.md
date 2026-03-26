---
name: security-review-owasp-dom-clobbering-prevention
description: Review HTML sanitization, named-property collisions, and browser global access patterns against OWASP DOM clobbering guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Dom Clobbering Prevention Security Review

Apply this skill to security reviews focused on Dom Clobbering Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on user-controlled HTML that can clobber globals, unsafe access to `window` and `document` properties, weak sanitization, and code that assumes named DOM properties are trustworthy. Prefer precise findings over broad advice.

## Scope

- HTML sanitization and templating paths that inject user-controlled markup into the DOM
- code that reads `window`, `document`, or implicit globals for URLs, callbacks, configuration, or security decisions
- protections such as strict mode, explicit declarations, type checks, object freezing, and named-property sanitization

## Review Goals

- Find places where attacker-controlled markup can overwrite security-relevant variables or object properties through DOM named-property collisions.
- Prioritize unsanitized HTML and unsafe global-property reads over lower-impact browser hardening issues.
- Produce findings with concrete evidence from code, headers, browser behavior, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every path that inserts HTML or derives behavior from `window`, `document`, or implicit globals.
2. Trace whether attacker-controlled attributes such as `id` or `name` can influence those reads.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they permit code execution, redirect control, or attacker influence over security-critical browser state.

## Review Checklist

### Sanitization and DOM Insertion

- Check that user-controlled HTML is sanitized before insertion into the DOM.
- Check whether sanitizers strip, rewrite, or namespace attacker-controlled `id` and `name` attributes.
- Flag HTML insertion patterns that allow named-property collisions without any sanitization boundary.

### Global Access and Type Validation

- Check code paths that read values from `window.foo`, `document.foo`, or undeclared globals before using them in URLs, script loading, or routing.
- Check that these reads validate type and origin expectations rather than trusting browser named-property resolution.
- Flag code that treats DOM-clobberable properties as authoritative configuration or callback targets.

### Scope Control and Hardening

- Check that variables are explicitly declared and scoped rather than leaking to the global object.
- Check whether sensitive configuration objects are frozen or otherwise protected from mutation where practical.
- Flag architectures that unnecessarily expose security-critical state through globally reachable names.

### Framework and Library Integration

- Check DOMPurify, Sanitizer API, or equivalent configuration for named-property protection features where used.
- Check whether framework wrappers or custom components reintroduce unsafe HTML insertion after nominal sanitization.
- Check tests for named-property collision abuse rather than only standard XSS payloads.

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

- Critical: User-controlled markup can clobber security-critical globals and drive code execution, redirect control, or equivalent high-impact behavior.
- High: Unsafe reads from clobberable DOM properties influence script sources, callback targets, or sensitive routing decisions.
- Medium: Sanitization or scoping gaps increase exposure but need additional conditions to become exploitable.
- Low: Hardening opportunities such as freezing objects or stricter declarations with stronger core protections otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects sanitization, named-property resolution, global access, or library integration.
- Mention the exact property, global variable, or HTML insertion point involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
