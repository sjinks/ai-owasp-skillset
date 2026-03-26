---
name: security-review-owasp-session-management
description: Review session handling, cookies, timeouts, lifecycle transitions, and hijacking defenses against OWASP session management guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Session Management Security Review

Apply this skill to security reviews focused on Session Management concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on session hijacking, fixation, weak cookie policy, timeout failures, client-side token storage, and incomplete logout or reauthentication behavior. Prefer precise findings over broad advice.

## Scope

- session identifier generation, format, validation, and exchange mechanisms
- cookie attributes such as Secure, HttpOnly, SameSite, Domain, Path, and persistence
- TLS enforcement and mixed-protocol handling for authenticated flows
- login, privilege change, password reset, reauthentication, and logout session transitions
- idle timeout, absolute timeout, renewal, invalidation, and cache-clearing behavior
- localStorage, sessionStorage, Web Worker, and browser cache use for session data
- anomaly detection, session binding signals, and lifecycle logging

## Review Goals

- Find weaknesses that let attackers guess, fix, steal, replay, or prolong authenticated sessions.
- Prioritize session hijacking, fixation, and expiration failures over weaker hardening issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify where authenticated state is created, resumed, renewed, elevated, and destroyed.
2. Trace the session identifier across cookies, headers, storage, caches, and server-side session state.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by takeover risk, replayability, and whether the weakness survives across logout or timeout boundaries.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing session creation, renewal, invalidation, and browser exposure end to end.

## Review Checklist

### Session ID Properties

- Check that session identifiers are generated with a cryptographically secure RNG and enough entropy.
- Flag meaningful session ID content such as user identifiers, roles, timestamps, or other predictable structure.
- Check whether the application accepts attacker-supplied session IDs it did not generate.
- Check whether session IDs are treated as untrusted input before lookup or parsing.
- Flag exposure of session identifiers in URLs, logs, analytics, or referrer-bearing flows.

### Cookie and Transport Security

- Check whether session cookies use Secure, HttpOnly, and an intentional SameSite setting.
- Check whether Domain and Path are scoped narrowly enough to reduce sibling-site and subdomain abuse.
- Flag persistent session cookies where the risk model calls for non-persistent session state.
- Confirm authenticated flows stay on TLS from pre-login through logout and that IDs are not reused across HTTP to HTTPS transitions.
- Check HSTS and other transport protections that reduce downgrade or mixed-content exposure.

### Session Lifecycle and Fixation Resistance

- Check whether the session ID is regenerated after authentication.
- Check whether the session ID is regenerated after privilege elevation, password change, or other major identity-state changes.
- Flag permissive fixation behavior where an attacker can pre-seed a valid session identifier.
- Check whether old session identifiers are invalidated after renewal.
- Check whether reauthentication is required after risk events or sensitive actions.

### Expiration, Logout, and Cleanup

- Check whether idle timeout and absolute timeout are enforced server-side rather than by client timers.
- Check whether logout destroys server-side session state, clears client cookies, and prevents reuse.
- Check whether sensitive responses disable caching and whether logout uses cleanup mechanisms such as Clear-Site-Data where appropriate.
- Check whether remembered or long-lived sessions use stronger renewal and invalidation controls.

### Client-Side Storage and Browser Behavior

- Flag use of localStorage for session tokens unless the design clearly accepts the XSS and persistence tradeoff.
- Check whether sessionStorage, Web Workers, or similar mechanisms still leak secrets to the main document or untrusted scripts.
- Check whether browser history, back-button caching, or cross-tab reuse can expose sensitive post-auth data.

### Detection and Monitoring

- Check whether the application logs session creation, renewal, destruction, invalid-session use, and anomalous changes.
- Check for brute-force or guessing detection on invalid session identifiers.
- Check whether client-property binding or anomaly detection is used carefully as a detection aid rather than as a brittle primary control.

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

- Critical: Predictable or attacker-fixable session IDs, missing regeneration on authentication, or transport failures that expose live session identifiers.
- High: Reusable stolen sessions, missing server-side invalidation, persistent URL-based IDs, or logout behavior that leaves sessions active.
- Medium: Important timeout, SameSite, scope, cache-control, or logging gaps that increase exposure but do not independently grant takeover.
- Low: Narrow hardening or observability issues with limited direct exploitability.

## Findings Format Rules

- Always state whether the finding enables fixation, hijacking, replay, or prolonged session survival.
- Prefer root causes such as weak lifecycle handling over listing every affected endpoint separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
