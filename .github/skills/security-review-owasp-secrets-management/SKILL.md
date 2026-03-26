---
name: security-review-owasp-secrets-management
description: Review secret storage, access control, lifecycle automation, CI/CD handling, and incident readiness against OWASP secrets management guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Secrets Management Security Review

Apply this skill to security reviews focused on Secrets Management concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on secret sprawl, hardcoded credentials, over-broad access, weak rotation and revocation, unsafe CI/CD injection, and poor incident response readiness. Prefer precise findings over broad advice.

## Scope

- where secrets are stored, injected, consumed, cached, rotated, and revoked
- secrets manager selection, encryption, IAM scoping, and environment separation
- CI/CD pipelines, deployment tooling, runners, containers, and orchestrators that receive secrets
- in-memory handling, logs, backups, and client-side or disk-based leakage paths
- secret discovery, incident response, and break-glass procedures
- cloud and multi-cloud secret-management architecture and operational boundaries

## Review Goals

- Find weaknesses that expose secrets directly or make secret compromise hard to detect, contain, rotate, or recover from.
- Prioritize plaintext exposure, wildcard access, pipeline leakage, and long-lived credentials over lower-impact governance gaps.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every secret class in scope, such as API keys, database credentials, TLS keys, tokens, and signing material.
2. Trace how each secret is generated, stored, injected, used, rotated, logged, backed up, and revoked.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by blast radius, reuse window, and how easily a compromise could spread or persist.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing storage, injection, IAM scoping, and rotation paths end to end.

## Review Checklist

### Storage and Encryption

- Check whether secrets are centralized in an intentional secrets-management system rather than scattered across source control, configs, or ad hoc stores.
- Check encryption at rest and in transit for stored and delivered secrets.
- Flag plaintext secrets in repositories, images, manifests, build outputs, or local configuration defaults.
- Check whether backup and restore paths protect secrets at least as strongly as production systems.

### Access Control and IAM

- Check whether users, workloads, and pipelines receive least-privilege access to only the secrets they need.
- Flag wildcard or environment-spanning access patterns that increase lateral-movement risk.
- Check whether access is attributable to specific identities and environments.
- Check whether administrative operations such as create, rotate, and revoke are tightly controlled.

### Lifecycle, Rotation, and Revocation

- Check whether secret generation uses secure randomness and approved cryptography where relevant.
- Check whether each secret class has an owner, purpose, rotation strategy, and expiration expectations.
- Flag long-lived credentials where dynamic or shorter-lived secrets are feasible.
- Check whether revocation and rotation can be executed quickly during incident response.
- Check whether consumers can handle rotation without prolonged credential overlap.

### CI/CD and Runtime Injection

- Check whether pipelines inject secrets only at the point of need and avoid exposing them in logs, artifacts, or debug output.
- Flag runners or build environments that developers can exec into or otherwise inspect while secrets are present.
- Check whether pull requests, forks, or untrusted branches can access protected secrets.
- Check container, orchestrator, sidecar, or file-based injection patterns for least exposure and shortest retention.

### Detection, Logging, and Incident Response

- Check whether secret access, rotation, revocation, and suspicious bulk access are logged with strong attribution.
- Check for detection of committed secrets, leaked credentials, or misuse patterns across code and operational environments.
- Check incident playbooks, break-glass handling, and tested recovery procedures.
- Flag missing or weak logging where a compromise would be hard to investigate.

### In-Memory and Client-Side Handling

- Check whether applications avoid exposing secrets through immutable strings, shared logs, crash dumps, browser storage, or insecure local caches.
- Check whether transport of secrets always uses TLS or better and whether sensitive clients consider stronger delivery protections.
- Check whether high-sensitivity secrets remain in memory longer than necessary.

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

- Critical: Plaintext secret exposure, pipeline or runtime paths that expose production secrets broadly, or transport and storage failures that make theft trivial.
- High: Missing rotation or revocation for impactful secrets, overly broad IAM, or secret injection patterns that realistically leak credentials.
- Medium: Important lifecycle, audit, backup, or in-memory handling gaps that increase blast radius or delay response.
- Low: Governance and hardening gaps with limited immediate exploitability.

## Findings Format Rules

- State the secret class affected and whether the impact is disclosure, overexposure, weak lifecycle handling, or poor recovery readiness.
- Prefer root causes such as sprawl or wildcard access over listing every leaked location separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
