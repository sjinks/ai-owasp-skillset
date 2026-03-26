---
name: security-review-owasp-threat-modeling
description: Review system models, trust boundaries, STRIDE coverage, and mitigation ownership against OWASP threat modeling guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Threat Modeling Security Review

Apply this skill to security reviews focused on Threat Modeling concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on incomplete system models, missing trust boundaries, unexamined STRIDE categories, vague mitigation plans, and stale threat models that no longer match the implementation. Prefer precise findings over broad advice.

## Scope

- architecture models, DFDs, trust boundaries, external entities, processes, data stores, and integration paths
- identified threats, rankings, acceptance decisions, and mitigation plans connected to the actual implementation
- review cadence, ownership, and change triggers that keep the threat model current as the system evolves

## Review Goals

- Find places where the system model is incomplete, major threat classes are skipped, or mitigations are too vague to implement or test.
- Prioritize missing threats around trust boundaries, privilege escalation, information disclosure, and high-impact misuse over formatting or process trivia.
- Produce findings with concrete evidence from prompts, code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the system model, DFD elements, trust boundaries, and major data flows in scope.
2. Compare the model against the actual architecture, code, infrastructure, and integrations.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they hide high-impact threats, prevent measurable mitigations, or leave critical changes unmodeled.

## Review Checklist

### System Model Validation

- Check that DFDs or equivalent models reflect actual processes, data stores, external entities, and trust boundaries.
- Check that cloud services, managed components, ephemeral workloads, and external integrations are represented.
- Flag missing or oversimplified model elements for critical data flows or privileged operations.

### Threat Identification and Coverage

- Check that STRIDE categories are considered systematically for relevant model elements.
- Check spoofing, tampering, repudiation, information disclosure, denial of service, and privilege escalation threats at each major trust boundary.
- Flag threat models that list generic risks without tying them to specific flows, assets, or attacker outcomes.

### Mitigation and Acceptance Strategy

- Check that each meaningful threat has a concrete response: mitigate, eliminate, transfer, or accept.
- Check that mitigations are buildable, testable, and assigned to owners rather than framed as vague intentions.
- Flag risk acceptance without business justification, owner, or expiry or reevaluation criteria.

### Review Cadence and Change Triggers

- Check that major architecture, trust-boundary, protocol, or deployment changes trigger updates to the threat model.
- Check that developers, architects, and security reviewers all participate in sign-off where the system warrants it.
- Check that the threat model remains accessible and actionable for code review, testing, and rollout decisions.

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

- Critical: Missing or incorrect threat modeling leaves high-impact trust-boundary threats unrecognized or unmitigated.
- High: STRIDE coverage, mitigation definition, or ownership is incomplete for important attack paths.
- Medium: Review cadence, ranking, or decomposition weaknesses reduce assurance but do not hide obviously critical threats.
- Low: Documentation, formatting, or minor modeling-consistency issues with otherwise usable threat coverage.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects system modeling, threat identification, mitigation strategy, or review cadence.
- Mention the specific trust boundary, data flow, or omitted threat category when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
