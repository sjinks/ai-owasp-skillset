---
name: security-review-owasp-third-party-javascript-management
description: Review Third-Party JavaScript Management concerns, trust boundaries, and operational assumptions against OWASP Third-Party JavaScript Management guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Third-Party JavaScript Management Security Review

Apply this skill to security reviews focused on Third-Party JavaScript Management concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on DOM injection, unsafe client-side state, cross-origin trust mistakes, browser policy bypasses, framing and leakage issues, and unsafe third-party script behavior. Prefer precise findings over broad advice.

## Scope

- browser-rendered pages, front-end code, DOM sinks, storage, and client-origin interactions related to this topic
- response headers, browser policies, framing rules, script loading, and client-side trust boundaries
- embedded third-party code, extensions, widgets, and browser APIs that expand attacker influence

## Review Goals

- Find weaknesses that let attacker-controlled content execute, leak sensitive data, or subvert browser trust decisions.
- Prioritize DOM injection, policy bypass, framing, cross-origin leakage, and unsafe third-party code over cosmetic client issues.
- Produce findings with concrete evidence from code, headers, browser behavior, tests, documentation, or observable behavior.

## Review Procedure

1. Identify rendering paths, browser storage, script loading, and cross-origin interactions in scope.
2. Trace attacker-controlled data into DOM sinks, browser APIs, policies, and third-party integrations.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable script execution, credential theft, framing abuse, or cross-origin data leakage.

## Review Checklist

### Rendering and Client-Side Sinks

- Check whether attacker-controlled input reaches DOM APIs, template escape hatches, or other executable browser contexts.
- Check browser storage, postMessage, and local state handling for cross-origin or script-injection abuse paths.
- Flag assumptions that client-side checks, hidden fields, or browser-only controls provide server-grade security.

### Browser Policies and Framing

- Check CSP, framing, navigation, and origin policies for bypasses, weak defaults, or inconsistent deployment.
- Check mixed-content, sandbox, and permission boundaries where the topic depends on browser enforcement.
- Flag policy gaps that leave sensitive UI or state exposed to embedding, probing, or leakage.

### Third-Party and Ecosystem Risk

- Check third-party scripts, extensions, widgets, and browser features for excessive privilege and supply-chain exposure.
- Check dependency loading, integrity, update, and allowlisting practices where external code runs in trusted contexts.
- Check telemetry and incident controls for client-side abuse or content-tampering signals.

### Topic Focus Areas

- Review the browser trust boundaries, client-side state, and rendering behaviors most affected by this topic.
- Review unsafe interactions among DOM updates, storage, framing, messaging, and third-party content relevant to this topic.
- Review response-side defenses, browser-enforced controls, and frontend isolation mechanisms relevant to this topic.
- Review how user-controlled input, script execution, and client-side dependencies can weaken this topic.
- Review test coverage and telemetry for client-side failures or abuse paths related to this topic.

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

- Critical: Browser-side weaknesses that enable reliable script execution in sensitive contexts or broad credential/session theft.
- High: Policy bypasses, framing abuse, or cross-origin leakage with realistic attacker control over inputs or origins.
- Medium: Important hardening and boundary gaps that increase exposure but need additional conditions.
- Low: Defense-in-depth and observability issues with stronger client and server controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects rendering, policy enforcement, client storage, framing, or third-party script trust.
- Mention the browser-controlled boundary or sink involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
