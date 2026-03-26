---
name: security-review-owasp-vulnerable-dependency-management
description: Review dependency vulnerability detection, remediation strategy, temporary mitigations, and risk acceptance controls against OWASP vulnerable dependency management guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Vulnerable Dependency Management Security Review

Apply this skill to security reviews focused on Vulnerable Dependency Management concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on how teams detect, assess, patch, wrap, or accept vulnerable dependencies after disclosure, including transitive dependencies and temporary mitigation code. Prefer precise findings over broad advice.

## Scope

- dependency scanners, advisory sources, transitive dependency analysis, and triage workflows for discovered CVEs
- patch rollout, wrapper or mitigation code, test coverage, compensating controls, and exception management for unresolved issues
- ownership, formal risk acceptance, and long-term replacement plans for vulnerable components

## Review Goals

- Find weaknesses in the remediation workflow that leave vulnerable dependencies effectively unmanaged even after detection.
- Prioritize unresolved exploitable dependencies, untested patching, weak workaround code, and informal risk acceptance over tooling cosmetics.
- Produce findings with concrete evidence from code, configuration, manifests, tests, documentation, or observable behavior.

## Review Procedure

1. Identify how vulnerable dependencies are detected, triaged, patched, or deferred in the current workflow.
2. Trace one or more representative vulnerabilities from detection through remediation, workaround, or acceptance.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they leave known exploitable code in place without effective reduction of risk.

## Review Checklist

### Detection and Assessment Quality

- Check scanner coverage, advisory sources, transitive dependency visibility, and handling of false positives and false negatives.
- Check whether severity, exploitability, exposure, and reachability are assessed rather than relying only on raw CVSS values.
- Flag workflows that cannot distinguish public-entry exploitation from theoretical or unreachable cases.

### Patch and Workaround Execution

- Check whether patched versions are tested before rollout and whether compatibility changes are reviewed.
- Check whether temporary wrapper code or compensating controls actually cover all vulnerable call sites.
- Flag broad ignore rules, blanket suppressions, or mitigation claims without evidence.

### Unfixed Dependency Governance

- Check whether unresolved vulnerabilities have explicit owners, review dates, and formally accepted business risk where required.
- Check whether alternative components, replacement plans, or isolation strategies are underway for long-lived unfixed issues.
- Flag dependencies that remain vulnerable because ownership and replacement planning are absent.

### Evidence, Communication, and Regression Safety

- Check whether remediation rationale, temporary controls, and residual risk are visible to developers and reviewers.
- Check tests, logs, and alerts that would detect failure or regression of a workaround.
- Check whether deployment automation prevents reintroduction of known vulnerable versions.

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

- Critical: Known exploitable vulnerable dependencies remain reachable from exposed attack paths without effective remediation or compensating controls.
- High: Patch rollout, wrapper coverage, or acceptance governance is weak enough to leave substantial residual risk.
- Medium: Detection, triage, or communication weaknesses increase the chance of mishandling vulnerabilities but need additional failures to cause compromise.
- Low: Process and visibility issues with stronger remediation controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects detection, patching, workaround coverage, or risk acceptance.
- Mention the exact dependency, CVE handling path, or compensating-control gap involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
