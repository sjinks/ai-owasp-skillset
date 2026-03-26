---
name: security-review-owasp-server-side-request-forgery-prevention
description: Review outbound request features and URL handling against OWASP SSRF prevention guidance. Use for webhook targets, callback URLs, fetchers, and internal service request controls.
argument-hint: Optionally specify the outbound request flow, HTTP client, or files to focus on
---

# OWASP Server Side Request Forgery Prevention Security Review

Apply this skill to security reviews focused on Server Side Request Forgery Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on internal network reachability, cloud metadata exposure, redirect bypasses, parser confusion, DNS rebinding or pinning risks, and mismatched allowlist or blocklist strategies.

## Scope

Apply this skill to:

- webhook, callback, and user-supplied endpoint features
- outbound HTTP or HTTPS requests built from user-controlled domains, IPs, or URLs
- service integrations where user input influences the destination
- DNS resolution logic and hostname validation
- HTTP client redirect behavior and protocol restrictions
- egress filtering, metadata endpoint protection, and network-layer controls
- interactions between SSRF and upstream parser bugs such as XXE that can trigger outbound fetches

## Review Goals

- Find SSRF paths that let attackers reach internal services, metadata endpoints, or unintended external targets.
- Prioritize cloud credential exposure, internal network access, and redirect or DNS-based bypasses.
- Produce findings with concrete evidence from code, configuration, infrastructure policy, or observable behavior.

## Review Procedure

1. Identify every feature that performs outbound requests.
   Include webhook senders, URL fetchers, preview generators, importers, proxies, and integration callbacks.
2. Trace how user input influences the destination, protocol, redirect handling, and DNS resolution.
3. Determine whether the use case is a strict allowlist case or a broader external-request case with defense in depth.
4. Validate each relevant area against the checklist below.
5. Prioritize findings by internal reachability, credential exposure, and bypass realism.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for parser, DNS, redirect, and network-path review.

## Review Checklist

### Input Validation Strategy

- Check whether the implementation accepts only the minimal target representation needed, such as a domain or IP rather than an arbitrary URL when possible.
- Check whether the design clearly matches an allowlist case or a broader external-destination case.
- Flag naive string parsing or regex-only URL validation for complex target handling.
- Prefer validated parser output over raw input everywhere downstream.

### IP and Domain Validation

- Check whether validated parser output is used rather than the raw user input.
- Check whether both IPv4 and IPv6 forms are handled safely.
- Flag alternate encodings such as hex, octal, dword, shorthand, or other parser ambiguities when the language stack is susceptible.
- For allowlist cases, check whether parsed domains or IPs are compared against a strict allowlist.
- For broader external-destination cases, check whether resolved addresses are verified as public and non-internal.

### DNS and Redirect Handling

- Check whether DNS resolution is part of the defense where needed, especially for domain-based targets.
- Flag missing checks for DNS pinning or rebinding style bypasses.
- Check whether HTTP clients disable automatic redirect following or re-validate redirected targets before use.
- Flag code that validates one host and then follows redirects to another without re-checking.
- Check whether DNS responses are validated after resolution and before connection.

### Protocol and Request Construction

- Check whether outbound protocols are restricted to the minimum set, typically HTTP and HTTPS.
- Flag support for risky protocols or protocol confusion where the destination is user-controlled.
- Check whether request parameters and tokens are validated separately rather than accepting a fully user-controlled target URL.
- Check whether application logic can be abused indirectly through XML or document parsing paths that trigger server-side fetches.

### Network and Metadata Protection

- Check whether metadata endpoints and private address ranges are blocked.
- Check egress filtering, firewall rules, security groups, or service policies that constrain outbound traffic.
- Check cloud metadata protections such as IMDSv2 where applicable.
- Check whether metadata endpoints are blocked both by application logic and by network policy where feasible.

### Bypass Indicators

- Flag direct use of user input in request builders after superficial validation.
- Flag URL handling that mixes parsing, validation, and request execution in a way that obscures which value is trusted.
- Check whether failures in validation or resolution fall back to permissive behavior.
- Flag validation that occurs before a later transform, redirect, or canonicalization step changes the effective target.

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

- End with a short conclusion covering the dominant SSRF risk theme.

## Severity Guidance

- Critical: Reachable metadata endpoints, internal credential exposure, or realistic access to highly sensitive internal services.
- High: Redirect or DNS-based bypasses, acceptance of arbitrary URLs with weak validation, parser confusion that reaches internal targets, or reachable private network ranges.
- Medium: Important control gaps such as incomplete parser output validation, weak protocol restrictions, or missing defense-in-depth constraints with partial mitigations present.
- Low: Narrow hardening gaps with strong network and application controls clearly reducing exploitability.

## Findings Format Rules

- State clearly whether the issue is an allowlist failure, a blocklist failure, or a parser or redirect failure.
- Distinguish application-layer controls from network-layer controls.
- Mention whether the issue depends on DNS behavior, redirects, metadata access, or upstream parser-triggered fetches.
- Do not assume DNS protections or egress filters exist unless they are visible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- Do not produce a long narrative summary before listing findings.
