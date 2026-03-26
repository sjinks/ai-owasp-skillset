---
name: security-review-owasp-xs-leaks
description: Review framing controls, Fetch Metadata defenses, response normalization, and browsing-context isolation against OWASP XS-Leaks guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP XS Leaks Security Review

Apply this skill to security reviews focused on XS Leaks concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on cross-site information leakage through framing, timing, navigation, cache behavior, Fetch Metadata gaps, postMessage mistakes, and missing context isolation. Prefer precise findings over broad advice.

## Scope

- browser-visible state transitions and responses that can leak authentication or sensitive state across origins
- headers and controls such as `frame-ancestors`, COOP, CORP, SameSite, cache policy, and Fetch Metadata filtering
- postMessage, framing, navigation, and response-shape behaviors that reveal information without direct cross-origin reads

## Review Goals

- Find cross-site observable differences that leak whether a user is authenticated, belongs to a group, or can access a protected resource.
- Prioritize framing exposure, missing Fetch Metadata defenses, and observable response differences over generic browser hardening gaps.
- Produce findings with concrete evidence from code, headers, browser behavior, tests, documentation, or observable behavior.

## Review Procedure

1. Identify sensitive endpoints, framed resources, navigation targets, and state-dependent responses in scope.
2. Trace which of them can be observed cross-site through framing, timing, cache, message, or resource-loading side channels.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they leak sensitive state cross-site or weaken browser isolation around authenticated resources.

## Review Checklist

### Framing and Browsing Context Isolation

- Check that sensitive pages cannot be framed cross-site without explicit need.
- Check COOP and related browsing-context isolation settings where window or frame enumeration matters.
- Flag endpoints whose visible load success, frame count, or navigation behavior reveals sensitive state.

### Request Filtering and Messaging

- Check server-side Fetch Metadata handling for cross-site requests to sensitive endpoints.
- Check `postMessage` handlers for strict `targetOrigin` and origin validation.
- Flag resources that accept cross-site request contexts without additional gating even though they expose state.

### Response Shape, Timing, and Cache Behavior

- Check whether authenticated and unauthenticated states produce distinguishable status codes, content lengths, redirects, or timing patterns.
- Check cache policy and response reuse for auth-sensitive resources.
- Flag observable differences that permit account, membership, or workflow-state inference cross-site.

### Cookie and Resource Isolation Hardening

- Check SameSite cookie settings and whether they meaningfully reduce cross-site request side channels.
- Check CORP or equivalent resource isolation on sensitive static or semi-static responses.
- Check whether mitigations are layered rather than relying on a single header or browser default.

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

- Critical: Sensitive state can be inferred cross-site from protected endpoints with little effort and high confidence.
- High: Missing framing, Fetch Metadata, or message-origin controls materially enable XS-Leak techniques.
- Medium: Cache, cookie, or response-normalization gaps increase observability but need additional attacker conditions.
- Low: Isolation hardening and telemetry issues with stronger primary XS-Leak controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects framing, request filtering, response normalization, or cookie and resource isolation.
- Mention the exact side channel or observable difference involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
