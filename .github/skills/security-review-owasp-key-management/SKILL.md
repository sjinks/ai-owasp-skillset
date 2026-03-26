---
name: security-review-owasp-key-management
description: Review cryptographic key generation, storage, distribution, lifecycle controls, and trust-store handling against OWASP key management guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Key Management Security Review

Apply this skill to security reviews focused on Key Management concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on plaintext key exposure, weak algorithms or key sizes, bad storage boundaries, missing lifecycle controls, poor accountability, and unsafe trust-store handling. Prefer precise findings over broad advice.

## Scope

- key generation, selection, strength, and cryptographic module boundaries
- key storage, wrapping, backup, escrow, and recovery procedures
- key distribution and secure transport between systems or modules
- purpose separation, least privilege, and human access controls for key material
- trust stores, root-certificate handling, and integrity of trust anchors
- libraries, HSMs, vaults, and operational documentation supporting key management

## Review Goals

- Find weaknesses that expose cryptographic keys directly or make compromise hard to detect, contain, or recover from.
- Prioritize plaintext key exposure, weak key strength, and missing lifecycle controls over lower-impact documentation gaps.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the key types in scope, including signing keys, encryption keys, wrapping keys, trust anchors, and backup or escrow material.
2. Trace how each key is generated, stored, transported, used, rotated, backed up, and retired.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by direct exposure risk, compromise blast radius, and whether the weakness breaks confidentiality, integrity, or recovery.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing key generation, storage boundaries, transport, and lifecycle handling end to end.

## Review Checklist

### Generation and Algorithm Selection

- Check whether keys are generated using approved algorithms and strong randomness inside an appropriate cryptographic boundary.
- Check whether key length and algorithm choice match the sensitivity and lifetime of the protected data.
- Flag deprecated or weak algorithms and any unexplained deviation from current policy or standard guidance.

### Storage and Exposure Boundaries

- Check whether keys are stored in HSMs, vaults, wrapped form, or other isolated control planes rather than plaintext files, code, or ordinary config.
- Flag application paths that directly expose raw key material when cryptographic operations could stay inside a secure module or service.
- Check whether stored keys have integrity protection and whether wrapping keys are at least as strong as the keys they protect.
- Check whether human operators can retrieve plaintext keys unnecessarily.

### Distribution, Transport, and Trust Stores

- Check whether keys are transported only over authenticated and encrypted channels.
- Check whether offline export, backup, or escrow uses strong wrapping and controlled receiving endpoints.
- Check trust stores for root-certificate injection risk, integrity protections, and controlled update procedures.
- Check whether exported trust anchors or keys require authentication and authorization.

### Lifecycle, Backup, and Recovery

- Check whether there are documented and testable procedures for rotation, revocation, backup, escrow, and recovery.
- Flag missing compromise response plans that would delay re-keying after exposure.
- Check whether retention, archival, and backup behavior matches the intended key purpose.
- Check whether signing keys are treated differently from decryption or escrow-capable keys where appropriate.

### Accountability and Purpose Separation

- Check whether each key has a single, well-defined purpose rather than being reused across signing, encryption, MAC, or wrapping roles.
- Check least-privilege access and strong audit trails for all key-management operations.
- Check whether unusual access patterns are detectable and reviewable.
- Check whether operational documentation and ownership are sufficient to support incident response.

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

- Critical: Plaintext key exposure, weak cryptography protecting high-value assets, or missing recovery controls that make key compromise catastrophic.
- High: Keys accessible outside intended security boundaries, insufficient key strength, unsafe transport, or missing revocation and backup controls.
- Medium: Important audit, trust-store, documentation, or purpose-separation gaps that raise impact or delay response.
- Low: Maintenance and hardening issues with otherwise strong key-management boundaries.

## Findings Format Rules

- State whether the weakness affects generation, storage, transport, lifecycle, or trust stores.
- Mention the key class affected whenever possible.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
