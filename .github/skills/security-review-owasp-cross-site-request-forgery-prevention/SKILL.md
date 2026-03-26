---
name: security-review-owasp-cross-site-request-forgery-prevention
description: Review anti-CSRF controls, token handling, browser signal validation, and client-side request generation against OWASP CSRF guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Cross Site Request Forgery Prevention Security Review

Apply this skill to security reviews focused on Cross Site Request Forgery Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on missing or weak token defenses, browser-signal validation mistakes, permissive CORS, unsafe login flows, and client-side request generation that bypasses intended CSRF controls. Prefer precise findings over broad advice.

## Scope

- synchronizer token and double-submit cookie implementations
- custom request header strategies for AJAX and API requests
- SameSite cookie policy, Origin and Referer verification, and Fetch Metadata handling
- CORS settings that affect whether attacker sites can send state-changing requests
- login CSRF, pre-session handling, and session fixation interactions
- client-side code that constructs destinations, methods, or parameters from attacker-controlled data

## Review Goals

- Find state-changing requests that can be triggered without trustworthy proof the request originated from the legitimate site or client.
- Prioritize missing token validation, unsafe token handling, and client-side CSRF patterns over defense-in-depth gaps.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every state-changing request path, including forms, APIs, AJAX actions, login flows, and browser-initiated cross-origin entry points.
2. Trace how the server distinguishes legitimate same-site requests from attacker-triggered cross-site requests.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by transaction sensitivity, request reachability from an attacker-controlled origin, and whether XSS or CORS issues make bypass easier.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for full token, browser-signal, and CORS review.

## Review Checklist

### Token Generation and Validation

- Check that CSRF tokens are generated server-side with strong randomness.
- Check that tokens are bound to the correct session or request context and validated on the server.
- Flag CSRF tokens stored in URLs, query strings, or insecure locations that leak through logs or browser history.
- Flag naive double-submit cookie patterns without HMAC or equivalent binding.
- Check that token validation failures are enforced on every state-changing request path.

### Browser Signals and Cookie Policy

- Check whether SameSite is used intentionally as defense in depth rather than as the only CSRF control.
- Check Origin or Referer validation for sensitive state-changing endpoints.
- Check Fetch Metadata handling, including how `Sec-Fetch-Site` and related headers are enforced and what fallback logic exists.
- Flag fail-open behavior when browser-supplied anti-CSRF signals are missing.

### AJAX, APIs, and CORS

- Check whether custom request headers are required for browser-based API calls where appropriate.
- Check whether the frontend framework or client code actually attaches the expected CSRF headers or tokens.
- Flag CORS configurations that permit credentialed cross-origin requests too broadly.
- Check whether simple requests or legacy content types still reach dangerous state-changing handlers.

### Client-Side CSRF Patterns

- Flag client-side code that builds endpoints, methods, or parameters from attacker-controlled sources such as URL fragments, query strings, postMessage data, or window.name.
- Check whether client-side request builders limit requests to predefined safe endpoints and methods.
- Check whether frontend helpers can be subverted into sending authenticated requests to attacker-chosen targets.

### Login and Session Interaction

- Check login forms and pre-authentication flows for CSRF exposure.
- Check whether pre-session tokens are used where appropriate and whether session fixation is prevented after login.
- Check whether high-risk actions use stronger user-interaction or step-up defenses beyond baseline CSRF controls.

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

- Critical: Cross-site requests can perform high-value state changes with no effective request-origin proof and minimal preconditions.
- High: Missing or bypassable CSRF protection on authenticated state-changing operations, or CORS and client-side behavior that make bypass realistic.
- Medium: Important fallback, SameSite, token-binding, or login-flow gaps that increase exposure but require additional conditions.
- Low: Defense-in-depth or compatibility weaknesses with strong primary protections still clearly present.

## Findings Format Rules

- State whether the weakness affects form posts, AJAX requests, API calls, or client-side request generation.
- Mention the actual trust signal that is missing or bypassable, such as token validation, SameSite, Origin checks, or Fetch Metadata.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
