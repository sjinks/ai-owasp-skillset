---
name: security-review-owasp-json-web-token-for-java
description: Review JWT creation, validation, storage, sidejacking defenses, and revocation logic in Java applications against OWASP JWT guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP JSON Web Token for Java Security Review

Apply this skill to security reviews focused on JSON Web Token for Java concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on algorithm confusion, weak keys, unsafe claim handling, token theft and replay, missing revocation, and risky browser storage patterns in Java-based JWT systems. Prefer precise findings over broad advice.

## Scope

- JWT creation and verification code in Java services, filters, middleware, and authentication layers
- algorithm selection and enforcement, key length, key storage, and verifier configuration
- claims such as issuer, audience, subject, expiration, and custom authorization data
- token sidejacking defenses, context binding, and secure storage on the client side
- logout, denylist, token lifetime, refresh, and revocation workflows
- optional confidentiality controls for sensitive claim sets

## Review Goals

- Find JWT weaknesses that enable token forgery, replay, misuse of stolen tokens, or unsafe disclosure of claims.
- Prioritize verifier misconfiguration, weak secrets, and replayable stolen tokens over lower-impact hygiene issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify where JWTs are created, verified, refreshed, invalidated, and attached to requests.
2. Trace the token from issuer to client storage to verifier logic and revocation paths.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by forgery risk, replay risk, and whether the issue affects all tokens or only a subset of flows.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing verifier configuration, key handling, and revocation logic.

## Review Checklist

### Algorithm and Signature Verification

- Check that verification code explicitly enforces the intended algorithm rather than trusting token-provided values.
- Flag acceptance of `none` or equivalent algorithm-confusion behavior.
- Check that public-key and shared-secret verification paths are not accidentally mixed.
- Check whether JWT libraries are used in a way that pins issuer expectations instead of auto-discovering trust from attacker-controlled data.

### Key Strength and Secret Handling

- Check whether HMAC keys are generated from secure randomness and have sufficient length.
- Flag hardcoded secrets, string literals used as cryptographic keys, or secrets loaded from weak configuration paths.
- Check that signing and encryption keys are stored and rotated through appropriate secret-management mechanisms.
- Check that keys are not logged, serialized, or exposed through diagnostics.

### Claims and Token Lifetime

- Check validation of issuer, audience, expiration, not-before, and issued-at claims where applicable.
- Check whether authorization or role claims are trusted without sufficient issuer and signature validation.
- Flag excessive token lifetime or refresh behavior that materially increases replay windows.
- Check whether sensitive claims are unnecessarily exposed in plaintext JWT payloads.

### Sidejacking and Client Storage

- Check whether stolen tokens can be replayed from another browser or device without additional context checks.
- Check fingerprint or binding mechanisms where the design claims replay resistance.
- Flag insecure browser storage patterns, especially long-lived tokens in localStorage without compensating controls.
- Check whether tokens are sent as cookies without the CSRF defenses that choice requires.

### Revocation and Logout

- Check whether the design requires user-driven or administrator-driven revocation.
- If revocation is required, check denylist or equivalent invalidation behavior before protected requests are honored.
- Check logout behavior for both client-side token disposal and server-side replay prevention where applicable.

### Error Handling and Safe Failure

- Check whether invalid-token exceptions fail closed.
- Check whether error messages leak token contents, verifier configuration, or signing details.
- Check whether failed validation accidentally grants default or partial access.

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

- Critical: Algorithm confusion, missing signature enforcement, or weak signing keys that allow token forgery.
- High: Replayable stolen tokens, absent revocation where revocation is required, or unsafe trust in claims that drives authorization decisions.
- Medium: Excessive token lifetime, weak browser storage choices, or disclosure of sensitive claim data without direct forgery impact.
- Low: Hardening and observability gaps with strong verifier behavior otherwise in place.

## Findings Format Rules

- Mention whether the issue affects token creation, storage, verification, or revocation.
- State whether the impact is forgery, replay, disclosure, or authorization misuse.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
