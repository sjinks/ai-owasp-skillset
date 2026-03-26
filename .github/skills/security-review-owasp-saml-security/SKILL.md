---
name: security-review-owasp-saml-security
description: Review SAML message validation, XML signature handling, certificate trust, bindings, and replay defenses against OWASP SAML guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP SAML Security Review

Apply this skill to security reviews focused on SAML Security concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on signature-wrapping bypasses, protocol-message incompleteness, weak certificate trust, replay of assertions, and unsafe IdP-initiated SSO handling. Prefer precise findings over broad advice.

## Scope

- SAML AuthnRequest and Response processing, assertions, and metadata validation
- XML signature validation, XPath selection, schema handling, and wrapping-attack resistance
- binding implementation, transport security, replay prevention, and cache behavior
- certificate, key, and trust-store handling for IdP and SP relationships
- IdP-initiated SSO, RelayState validation, session establishment, and logout behavior

## Review Goals

- Find weaknesses that let attackers forge, replay, substitute, or misroute SAML assertions.
- Prioritize XML signature validation failures, protocol-message omissions, and signing-key trust mistakes over lower-impact operational gaps.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the IdP, SP, certificate, binding, and message-processing paths in scope.
2. Trace a complete login from AuthnRequest through Response validation, session creation, and logout or replay checks.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable forged assertions, replay, trust confusion, or session takeover.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing XML signature validation, trust configuration, and replay protections end to end.

## Review Checklist

### Message Integrity and XML Signature Validation

- Check that schema validation and parser hardening happen before security-relevant XML processing.
- Flag XPath or DOM selection patterns vulnerable to XML signature wrapping, especially loose element lookups.
- Check that signatures are validated against pinned or intentionally trusted keys rather than attacker-controlled `KeyInfo`.
- Check message confidentiality or assertion encryption where the deployment requires it.

### Protocol Message Completeness and Processing

- Check that AuthnRequest, Response, and Assertion elements include and validate required IDs, issuer information, recipient, audience, and `InResponseTo` values.
- Check timestamps, conditions, subject confirmation, and recipient binding.
- Flag acceptance of incomplete or weakly bound messages that could enable substitution or replay.

### Bindings, Replay, and IdP-Initiated SSO

- Check Redirect and POST binding implementations for safe transport, cache behavior, and uniqueness expectations.
- Check replay detection, short assertion lifetime, and OneTimeUse or equivalent controls where applicable.
- If IdP-initiated SSO is supported, check RelayState allowlisting and additional defenses for unsolicited responses.
- Check session creation to ensure SAML success cannot be combined with session fixation.

### Certificates, Keys, and Trust

- Check certificate strength, algorithm choices, revocation behavior, and lifetime.
- Check private-key protection for IdP or SP signing keys.
- Check that trust decisions are bounded to the intended IdP or SP rather than an overly broad CA or metadata trust model.
- Check key-rotation procedures and how trust changes are propagated.

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

- Critical: Signature-validation bypasses, missing protocol bindings that enable forged assertions, or signing-key compromise or trust errors that let attackers impersonate any user.
- High: Replayable assertions, unsafe IdP-initiated flows, weak certificate validation, or RelayState misuse with realistic exploitation.
- Medium: Important binding, cache, session, or operational trust gaps that raise exposure without fully bypassing SAML validation.
- Low: Hardening and lifecycle issues with strong assertion validation otherwise in place.

## Findings Format Rules

- State whether the weakness affects XML signature validation, protocol processing, replay protection, or trust management.
- Mention the exact message element, parser behavior, or certificate trust decision involved when possible.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
