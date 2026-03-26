---
name: security-review-owasp-oauth2
description: Review OAuth 2 code flows, PKCE, redirect handling, token replay defenses, and client authentication against OWASP OAuth 2 guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP OAuth 2 Security Review

Apply this skill to security reviews focused on OAuth 2 concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on implicit-flow usage, missing PKCE, weak redirect validation, token replay, weak state handling, over-broad scopes, and unsafe client authentication. Prefer precise findings over broad advice.

## Scope

- authorization code flows with or without PKCE, for public and confidential clients
- redirect URI validation, state and nonce handling, and multi-authorization-server mix-up defenses
- access token and refresh token issuance, replay prevention, sender-constraining, and audience restriction
- client authentication mechanisms such as mTLS, private_key_jwt, and shared-secret fallbacks
- prohibited or legacy grant types such as implicit and resource owner password credentials

## Review Goals

- Find weaknesses that expose authorization codes or tokens, enable replay, or let attackers subvert the authorization flow.
- Prioritize implicit flow, missing PKCE, redirect misuse, token replay, and weak client authentication over lower-impact scope hygiene issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the authorization server, clients, resource servers, grant types, and trust boundaries in scope.
2. Trace the flow of authorization codes, state, redirect URIs, access tokens, and refresh tokens.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by token exfiltration risk, replay risk, and whether the flaw affects all clients or only a subset.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing end-to-end flow integrity, redirect handling, and token lifecycle controls.

## Review Checklist

### Authorization Code and PKCE

- Check that the authorization code flow is used instead of the implicit grant.
- For public clients, check that PKCE is mandatory with S256 rather than plain or optional verification.
- Check that authorization codes are single-use and short-lived.
- Flag token issuance in URL fragments or front-channel flows that bypass the token endpoint.

### Redirects, State, and Mix-Up Defense

- Check redirect URIs against exact allowlists rather than weak pattern matching.
- Flag open redirectors or dynamically accepted redirect targets.
- Check that state is unpredictable, user-agent bound, and validated before token use.
- In multi-authorization-server scenarios, check issuer validation or distinct redirect URIs to prevent mix-up attacks.

### Token Replay and Scope Control

- Check whether bearer tokens are tightly scoped to the intended audience and least privilege.
- Check sender-constraining mechanisms such as DPoP or mTLS where the deployment warrants replay protection.
- Check refresh token rotation or other replay-resistance controls.
- Flag long-lived, replayable tokens without strong compensating controls.

### Client Authentication and Forbidden Grants

- Check confidential-client authentication for strong methods such as mTLS or private_key_jwt where feasible.
- Flag weak shared-secret handling and secrets exposed in URLs or logs.
- Flag use of the resource owner password credentials grant.
- Check that client identity cannot be spoofed or switched during token redemption.

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

- Critical: Implicit grant use, missing PKCE on public clients, or redirect flaws that allow code or token exfiltration.
- High: Replayable tokens, weak state or nonce handling, weak client authentication, or missing audience restriction with realistic abuse paths.
- Medium: Over-broad scope, weak token lifetime choices, or incomplete sender-constraining where other defenses still exist.
- Low: Hardening and interoperability issues with otherwise strong OAuth 2 controls.

## Findings Format Rules

- State whether the weakness affects authorization code handling, redirects, tokens, or client authentication.
- Mention whether the impact is code theft, token replay, privilege expansion, or authorization-flow confusion.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
