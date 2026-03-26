---
name: security-review-owasp-multifactor-authentication
description: Review MFA enforcement, OTP handling, recovery flows, factor replacement, and adaptive-challenge logic against OWASP MFA guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Multifactor Authentication Security Review

Apply this skill to security reviews focused on Multifactor Authentication concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on MFA bypass through weak recovery, OTP replay or brute force, unsafe factor changes, inconsistent MFA enforcement across entry points, and unsafe fallback channels. Prefer precise findings over broad advice.

## Scope

- when MFA is required at login, step-up actions, and sensitive account-management changes
- OTP generation, TTL, rate limiting, single-use enforcement, storage, and logging behavior
- factor enrollment, factor replacement, reset, and recovery-code handling
- fallback channels such as SMS, email, certificates, passkeys, and backup factors
- adaptive or risk-based MFA logic and consistency across web, API, mobile, and SSO entry points

## Review Goals

- Find weaknesses that let attackers bypass MFA through recovery, replay valid OTPs, brute-force codes, or silently replace enrolled factors.
- Prioritize recovery and factor-change bypasses over lower-impact usability gaps.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify all authentication and step-up paths where MFA is expected.
2. Trace enrollment, challenge, failure, fallback, reset, and factor-change flows.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they permit full bypass, replay, or takeover through factor replacement or recovery.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing challenge enforcement, recovery assurance, and factor lifecycle controls end to end.

## Review Checklist

### MFA Requirement and Coverage

- Check that MFA is consistently enforced for the intended user classes and entry points.
- Check MFA requirements on sensitive actions such as password changes, email changes, MFA disablement, and privilege elevation.
- Flag gaps where APIs, mobile flows, or alternate login paths bypass MFA.

### OTP Handling and Storage

- Check that OTPs are generated with strong randomness where applicable, are time-limited, and are single-use.
- Check rate limiting and retry handling for OTP challenges.
- Flag plaintext OTP storage or OTPs written to logs, metrics, or traces.
- Check whether OTP verification uses safe comparison and whether challenge reuse is prevented.

### Recovery, Reset, and Factor Change

- Check recovery-code issuance, storage, and use.
- Flag recovery mechanisms that rely only on weak signals such as email possession without stronger identity proof where the risk requires it.
- Check that changing or disabling a factor requires reauthentication with an existing trusted factor rather than only a live session.
- Check for out-of-band notification and reviewable audit trails when factors are changed or reset.

### Factor Quality and Adaptive Controls

- Check factor choices against the application risk profile, especially when SMS or email are used.
- Check whether passkeys, hardware-backed factors, or stronger alternatives are supported where appropriate.
- Check adaptive MFA or risk-based logic for spoofable signals, unsafe fallback behavior, and weak overrides.
- Check whether third-party MFA services, libraries, or push channels introduce inconsistent or less secure paths.

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

- Critical: Recovery or factor-change flows that bypass MFA entirely, or OTP handling that allows replay or trivial compromise.
- High: Missing MFA on important entry points, weak fallback channels for high-risk accounts, or unbounded OTP brute-force exposure.
- Medium: Incomplete notifications, weaker-than-ideal factor choices, or adaptive-control gaps where stronger primary controls still exist.
- Low: Usability and hardening issues that do not materially reduce MFA assurance by themselves.

## Findings Format Rules

- State whether the weakness affects challenge enforcement, OTP handling, recovery, or factor lifecycle.
- Mention whether the impact is full MFA bypass, replay, brute force, or factor takeover.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
