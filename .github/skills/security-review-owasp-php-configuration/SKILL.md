---
name: security-review-owasp-php-configuration
description: Review PHP Configuration concerns, trust boundaries, and operational assumptions against OWASP PHP Configuration guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP PHP Configuration Security Review

Apply this skill to security reviews focused on PHP Configuration concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on unsafe defaults, privilege sprawl, exposed management surfaces, dependency risk, tenancy mistakes, and weak deployment boundaries. Prefer precise findings over broad advice.

## Scope

- framework, runtime, platform, infrastructure, and deployment controls related to this topic
- service, container, tenant, node, pipeline, and dependency trust boundaries that influence exposure
- operational automation, defaults, and ecosystem integrations that determine how securely the platform runs in practice

## Review Goals

- Find weaknesses that expand attacker reach through unsafe defaults, excessive privilege, boundary failures, or insecure supply-chain and deployment practices.
- Prioritize exposed control planes, weak isolation, dependency compromise risk, and privileged runtime paths over lower-impact hygiene issues.
- Produce findings with concrete evidence from code, configuration, manifests, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the runtime components, control planes, dependencies, and trust boundaries in scope.
2. Trace how code, configuration, identities, and privileges move through the platform or framework.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable environment compromise, tenant escape, dependency abuse, or privileged control-plane access.

## Review Checklist

### Runtime and Privilege Boundaries

- Check runtime identities, service accounts, container or process privileges, and management access for least privilege.
- Check exposed admin surfaces, debug paths, metadata access, and privileged integrations.
- Flag defaults that grant broader file, network, or control-plane access than the feature requires.

### Configuration, Isolation, and Dependencies

- Check tenancy, namespace, environment, or workload isolation assumptions relevant to this topic.
- Check configuration hardening, secret exposure, and environment-specific drift across deployments.
- Check third-party packages, plugins, images, or modules for trust, pinning, provenance, and update discipline.

### Operational Safeguards and Assurance

- Check logging, monitoring, scanning, policy enforcement, and test coverage for the relevant deployment surfaces.
- Check CI/CD and release processes for unauthorized modification or insecure artifact promotion paths where applicable.
- Check incident containment and rollback options for compromised dependencies or platform components.

### Topic Focus Areas

- Review framework or platform defaults, exposed management surfaces, and privilege boundaries relevant to this topic.
- Review deployment, configuration, secret handling, and environment-isolation controls that materially affect this topic.
- Review dependency, plugin, image, or module trust assumptions that could weaken this topic.
- Review operational safeguards such as scanning, policy enforcement, hardening baselines, and rollback options for this topic.
- Review whether this topic is enforced consistently across local, CI/CD, staging, and production environments.

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

- Critical: Weaknesses that enable environment-wide compromise, control-plane takeover, or cross-tenant escape.
- High: Privilege sprawl, exposed management paths, or supply-chain weaknesses with realistic high-impact abuse.
- Medium: Important hardening, isolation, or verification gaps that require additional conditions to escalate.
- Low: Operational and visibility gaps with stronger primary platform controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects runtime privilege, isolation, dependency trust, deployment automation, or management exposure.
- Mention the specific framework, platform layer, or environment boundary involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
