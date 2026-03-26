---
name: security-review-owasp-content-security-policy
description: Review CSP delivery, strict script controls, framing rules, and violation reporting against OWASP CSP guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Content Security Policy Security Review

Apply this skill to security reviews focused on Content Security Policy concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on permissive policies, unsafe inline execution, weak nonce or hash handling, incomplete framing restrictions, and report-only or meta-tag deployments that create a false sense of protection. Prefer precise findings over broad advice.

## Scope

- CSP headers and fallback delivery mechanisms across HTML responses and relevant application routes
- nonce, hash, script-src, style-src, object-src, base-uri, frame-ancestors, form-action, and reporting configuration
- interactions between CSP and existing XSS, clickjacking, or third-party script loading practices

## Review Goals

- Find CSP deployments that fail to block inline code, unsafe script execution, clickjacking, or cross-origin policy abuse.
- Prioritize `unsafe-inline`, `unsafe-eval`, permissive wildcards, missing `frame-ancestors`, and weak nonce discipline over lower-impact directive completeness issues.
- Produce findings with concrete evidence from code, headers, browser behavior, tests, documentation, or observable behavior.

## Review Procedure

1. Identify where CSP is emitted, overridden, omitted, or delivered in report-only mode.
2. Trace how scripts, styles, forms, frames, and third-party resources are intended to execute under the current policy.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they preserve XSS exploitability, enable framing abuse, or leave policy bypasses in high-risk pages.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing effective policy delivery and directive coverage per route.

## Review Checklist

### Policy Delivery and Coverage

- Check that CSP is delivered as an HTTP response header on relevant pages rather than relying only on meta tags.
- Check that the enforced policy matches the intended scope across app routes, subapps, and error pages.
- Flag report-only deployments that were never promoted to enforcement for sensitive pages.

### Strict Script and Style Controls

- Check that `script-src` uses nonces or hashes and avoids `unsafe-inline` and `unsafe-eval`.
- Check nonce generation for per-response unpredictability and correct propagation to all intended scripts.
- Check that `object-src 'none'` and `base-uri` restrictions are present where appropriate.

### Framing, Navigation, and Reporting

- Check `frame-ancestors` and `form-action` restrictions for clickjacking and phishing-resistant navigation behavior.
- Check reporting endpoints, `report-uri` or `report-to`, and whether violations are observed and triaged.
- Flag wildcard or broad allowlist directives that make reporting noisy while leaving execution permissive.

### Compatibility and Exception Handling

- Check whether legacy compatibility exceptions or third-party script requirements are narrowly scoped and documented.
- Check whether policy relaxations are justified per page or feature rather than applied globally.
- Check whether developers rely on CSP as a substitute for fixing underlying XSS or injection sinks.

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

- Critical: CSP allows reliable inline or injected script execution on sensitive pages, or framing protections are absent where they are required.
- High: Weak nonce handling, permissive wildcards, `unsafe-eval`, or incomplete policy delivery materially reduce CSP value.
- Medium: Reporting, coverage, or exception-scoping gaps weaken visibility or consistency but do not fully preserve exploitation by themselves.
- Low: Hardening and cleanup issues with a fundamentally strict policy in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects policy delivery, script execution, framing, or reporting.
- Mention the exact directive, page scope, or exception path involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
