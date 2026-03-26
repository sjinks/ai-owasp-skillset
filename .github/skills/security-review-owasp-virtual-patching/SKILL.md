---
name: security-review-owasp-virtual-patching
description: Review Virtual Patching concerns, trust boundaries, and operational assumptions against OWASP Virtual Patching guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Virtual Patching Security Review

Apply this skill to security reviews focused on Virtual Patching concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on missing analysis, incomplete coverage, weak detection and response hooks, and process gaps that leave exploitable weaknesses undiscovered or uncontained. Prefer precise findings over broad advice.

## Scope

- review, design, threat analysis, logging, error-handling, and operational process controls related to this topic
- decision points where coverage, prioritization, and response quality determine residual risk
- documentation, playbooks, and automation that define how security work is actually performed

## Review Goals

- Find process and control gaps that let important attack paths go unmodeled, unreviewed, undetected, or badly handled in production.
- Prioritize missing coverage and broken escalation paths that materially reduce the value of security controls.
- Produce findings with concrete evidence from code, playbooks, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the workflow, decision points, and operational artifacts that implement this topic.
2. Trace how the team detects, reviews, prioritizes, logs, or responds to relevant risks.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they hide serious attack paths, weaken incident handling, or reduce review coverage in high-risk areas.

## Review Checklist

### Coverage and Risk Identification

- Check whether the relevant assets, abuse paths, dependencies, and trust boundaries are explicitly identified and reviewed.
- Check whether the process includes the highest-risk flows rather than only nominal or happy-path behavior.
- Flag missing ownership, missing review triggers, or stale analysis that leaves important changes unassessed.

### Detection, Evidence, and Response

- Check logging, alerting, error handling, escalation, and response quality for the relevant threat scenarios.
- Check whether evidence is preserved at the right layer to support triage and incident investigation.
- Flag gaps that suppress useful signals or expose sensitive details while still failing to support response.

### Operational Follow-Through

- Check whether findings lead to enforceable policy, automation, tests, or rollout controls rather than one-off documentation.
- Check measurement, review cadence, and exception handling where the topic depends on repeatable governance.
- Check whether mitigation guidance is actionable and aligned with the code and systems actually in scope.

### Topic Focus Areas

- Review the core operating assumptions, ownership model, and decision points that govern this topic.
- Review how this topic is reflected in logging, triage, escalation, and incident or review workflows.
- Review the evidence model for this topic, including required records, metrics, or review artifacts.
- Review exception handling, policy drift, and change-management paths that could weaken this topic over time.
- Review whether this topic leads to concrete enforcement, automation, or follow-up actions instead of documentation alone.

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

- Critical: Process or response gaps that leave high-impact attack paths effectively unreviewed or undetectable in production.
- High: Missing coverage, weak logging or escalation, or stale governance with realistic high-impact consequences.
- Medium: Important review, telemetry, or ownership gaps that weaken assurance but need additional failures to cause compromise.
- Low: Documentation and workflow hardening gaps with stronger primary controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects coverage, detection, response, or operational follow-through.
- Mention the missing artifact, workflow step, or telemetry point when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
