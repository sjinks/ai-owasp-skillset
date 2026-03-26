---
name: security-review-owasp-attack-surface-analysis
description: Review exposed interfaces, trust boundaries, data exposure, and attack-surface growth against OWASP attack surface analysis guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Attack Surface Analysis Security Review

Apply this skill to security reviews focused on Attack Surface Analysis concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on untracked entry points, forgotten interfaces, unclassified sensitive data, hidden trust relationships, and attack-surface expansion caused by new features, roles, or integrations. Prefer precise findings over broad advice.

## Scope

- external and internal entry points, exit points, APIs, file handlers, admin paths, and network-facing services
- data stores, backups, offline media, secrets, integrations, and trust relationships that enlarge the reachable surface
- feature, role, protocol, or deployment changes that add new exposed behaviors without equivalent reassessment

## Review Goals

- Find exposed interfaces, privileged pathways, or sensitive resources that are reachable but not explicitly tracked or defended.
- Prioritize new external attack points, privilege-bearing interfaces, and overlooked high-value data over documentation-only gaps.
- Produce findings with concrete evidence from code, playbooks, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Inventory interfaces, services, user roles, data paths, and infrastructure components in scope.
2. Trace which of those are externally reachable, indirectly reachable, privileged, or sensitive.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they expose new attack paths, expand attacker reach across trust boundaries, or increase the blast radius of compromise.

## Review Checklist

### Entry and Exit Point Mapping

- Check that forms, APIs, file uploaders, admin actions, callbacks, background processors, and protocol handlers are inventoried.
- Check that anonymous, authenticated, privileged, and machine-to-machine entry points are distinguished.
- Flag interfaces that are reachable in production but absent from the review baseline or architecture records.

### Data and Resource Classification

- Check where secrets, PII, business-critical records, backups, and integration credentials reside or transit.
- Check that cross-system trust relationships and privileged resource access are explicitly mapped.
- Flag high-value data or offline resources that expand impact but are not considered part of the active surface.

### Risk Prioritization and Compensating Controls

- Check that high-risk areas such as authentication, session handling, crypto, upload, and custom protocol handling are ranked higher than generic endpoints.
- Check whether compensating controls such as WAFs, segmentation, proxies, or additional authentication materially reduce exposure.
- Flag risk models that count interfaces but ignore attacker effort, privilege change, or data sensitivity.

### Change Impact and Surface Growth

- Check whether new features, services, roles, integrations, or deployment changes trigger a fresh surface review.
- Check whether legacy endpoints, compatibility interfaces, or test-only paths remain reachable after product evolution.
- Check whether attack surface reduction opportunities such as role consolidation or interface removal are ignored.

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

- Critical: New or existing external attack points expose privileged actions or sensitive data with no meaningful review or protection.
- High: Important interfaces, roles, or trust relationships are omitted from the attack-surface baseline or materially underestimated.
- Medium: Change-tracking, classification, or compensating-control gaps increase exposure but do not by themselves prove exploitable access.
- Low: Documentation and visibility gaps with otherwise sound surface control.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects exposed interfaces, data classification, risk ranking, or change management.
- Mention the specific interface, user role, trust boundary, or data class involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
