---
name: security-review-owasp-authentication
description: Review authentication-related code, flows, and configuration against OWASP authentication guidance. Use for login, signup, password handling, MFA, reauthentication, account recovery, and brute-force defenses.
argument-hint: Optionally specify the auth flow, framework, or files to focus on
---

# OWASP Authentication Security Review

Apply this skill to security reviews focused on Authentication concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on vulnerabilities, exploit paths, and missing controls in authentication-related code and configuration. Prefer precise findings over broad advice.

## Scope

Apply this skill to:

- login and logout flows
- signup and account activation
- password storage, validation, reset, and change flows
- MFA enrollment, challenge, recovery, and bypass paths
- reauthentication and step-up authentication
- session transitions after authentication state changes
- account recovery and email address change flows
- brute-force protections and auth monitoring
- federation or external identity integrations such as OAuth, OIDC, SAML, passkeys, or client certificates

## Review Goals

- Find vulnerabilities in authentication design, implementation, configuration, and operational controls.
- Prioritize exploitable issues such as account takeover, user enumeration, brute-force exposure, weak password handling, and unsafe recovery flows.
- Produce findings with concrete evidence from the code or configuration under review.

## Review Procedure

1. Identify the authentication surface.
   Include login, signup, password reset, password change, MFA enrollment and challenge flows, reauthentication paths, email change flows, session transitions, and any machine or internal accounts.
2. Trace how credentials and authentication decisions move through the system.
   Check controllers, middleware, identity provider integration, backend services, templates, API responses, and logs.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by exploitability and impact.
   Account takeover, auth bypass, credential exposure, and enumeration that materially improves attack success should rank above hygiene issues.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing end-to-end login, recovery, reauthentication, and federation behavior.

## Review Checklist

### Identity and Account Boundaries

- Verify user identifiers are not predictable where exposure would increase risk.
- Check whether usernames can be email addresses only after verification, and whether alternate usernames are handled safely.
- Flag any exposure of internal, service, middleware, database, or otherwise sensitive accounts through public authentication interfaces.
- Flag reuse of an internal authentication solution for lower-trust or public access without proper isolation.

### Password Policy and UX

- Check minimum password length.
  Treat passwords shorter than 8 characters as weak when MFA is enabled, and shorter than 15 characters as weak when MFA is not enabled.
- Check maximum password length.
  The application should allow at least 64 characters to support passphrases.
- Flag silent password truncation.
- Flag password composition rules that unnecessarily require specific character classes.
- Confirm the system allows all characters, including whitespace and Unicode, where the platform can safely support them.
- Check whether breached or common passwords are blocked.
- Check whether the UI helps users choose strong passwords, such as with a strength meter.
- Confirm users can paste into username, password, and MFA fields, and that the login flow does not break password managers without a strong reason.

### Password Storage and Verification

- Check that passwords are stored with a dedicated password hashing approach rather than reversible encryption or unsalted general-purpose hashes.
- Check that password verification uses framework or language primitives intended for password validation.
- If custom comparison exists, flag missing constant-time behavior, missing explicit type handling, or missing input length limits.
- Check whether very long credential inputs can trigger denial-of-service conditions.

### Transport and Session Coupling

- Confirm login and all authenticated pages are served only over TLS or another strong transport.
- Flag any flow where credentials or authenticated session identifiers can traverse unencrypted channels.
- Check whether session identifiers or tokens are rotated after authentication state changes, especially reauthentication or recovery.

### Sensitive Changes and Recovery

- For password changes, confirm the user has an active authenticated session and must verify the current password unless a stronger recovery flow applies.
- Review forgot-password and other recovery flows for token entropy, expiration, single use, and generic responses.
- For email address changes, check whether the flow verifies the current session and requires MFA or current-password proof depending on account assurance.
- Check whether pending email changes require confirmation and generate notifications to the existing address.

### Reauthentication and Risk Events

- Check whether sensitive actions such as changing password, email, payment details, or trusted devices require reauthentication.
- Check whether reauthentication is triggered after risk events such as account recovery, password reset, suspicious logins, or device enrollment.
- Review whether adaptive or risk-based authentication decisions are tied to clear signals and safe outcomes such as MFA step-up, challenge, block, or session revocation.

### Error Handling and Enumeration Resistance

- Flag login, registration, password reset, and recovery responses that disclose whether an account exists, is disabled, or has a specific failure condition.
- Check whether HTTP status codes, redirects, URLs, or response body differences leak account validity.
- Check for timing discrepancies between existing-account and non-existing-account paths that could support enumeration.

### Automated Attack Defenses

- Check whether MFA is available or enforced where appropriate.
- Review login throttling controls, including threshold, observation window, and lockout duration.
- Prefer account-centric throttling over IP-only throttling.
- Check whether lockout logic can be abused for denial of service and whether recovery is handled safely.
- Treat CAPTCHA as defense in depth only, not as the sole automated-attack control.

### Logging and Monitoring

- Check whether authentication failures, password failures, and account lockouts are logged.
- Check whether logs are reviewable and useful for detection without exposing secrets.
- Flag logs that store plaintext credentials, password reset tokens, MFA secrets, or other sensitive authentication artifacts.

### Protocol and Federation Review

- If the system uses OAuth, OIDC, SAML, FIDO2, passkeys, or client certificates, inspect protocol-specific validation and trust boundaries instead of assuming the framework is secure by default.
- For OIDC, verify ID token validation including issuer, audience, signature, and expiration.
- Flag attempts to use OAuth alone as proof of authentication without the identity guarantees of OIDC or an equivalent mechanism.

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
OWASP basis: <relevant topic from this skill, such as password policy, enumeration resistance, reauthentication>
```

### 2. Review Gaps

- List only the important places where evidence is missing and a conclusion cannot be reached.
- Keep each item short and explicit about what was not visible.

Use this format:

```markdown
- Could not verify <control or flow> because <missing evidence>.
```

### 3. Passed Checks

- Summarize only notable controls that were clearly implemented correctly.
- Keep this section brief. Do not let it overshadow findings.

Use this format:

```markdown
- <control> appears correctly implemented based on <evidence>.
```

### 4. Overall Assessment

- End with a short conclusion covering the dominant risk theme.
- Mention whether the primary risk is account takeover, enumeration, brute-force exposure, insecure recovery, federation misuse, or insufficient evidence.

## Severity Guidance

- Critical: Authentication bypass, credential disclosure at scale, or direct account takeover with minimal prerequisites.
- High: Realistic account compromise, recovery abuse, or strong enumeration or brute-force weaknesses that substantially improve attack success.
- Medium: Important missing controls that increase exposure but do not independently yield compromise.
- Low: Hardening gaps or weak patterns with limited direct exploitability.

## Findings Format Rules

- Do not speculate about hidden protections.
- Do not dilute a finding with generic framework advice.
- Tie each recommendation to the specific weakness you observed.
- Prefer exact evidence over broad file summaries.
- If multiple code locations implement the same flaw, group them into one finding when they share the same root cause.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- Prefer actionable review findings over generic advice.
- Do not produce a long narrative summary before listing findings.
