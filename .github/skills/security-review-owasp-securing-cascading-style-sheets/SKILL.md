---
name: security-review-owasp-securing-cascading-style-sheets
description: Review CSS access control, selector information leakage, and user-controlled styling risks against OWASP CSS security guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Securing Cascading Style Sheets Security Review

Apply this skill to security reviews focused on Securing Cascading Style Sheets concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on CSS that leaks sensitive feature intent, weak access controls over role-specific stylesheets, and user-controlled styling that can support overlay, clickjacking, or reconnaissance attacks. Prefer precise findings over broad advice.

## Scope
- stylesheet organization, access control, build-time class naming, and role- or feature-specific CSS exposure
- user-controlled HTML or CSS paths that can influence layout, overlays, or visual trust signals
- selector naming, CSS delivery, and frontend architecture choices that reveal internal capability or authorization information

- code paths, configuration, infrastructure, tests, and workflows related to this topic
- trust boundaries, dependencies, integrations, and operational controls influenced by this topic
- documentation or security assumptions that materially affect how the controls are implemented

## Review Goals
- Find places where CSS exposes sensitive functionality, role boundaries, or exploitable styling control to attackers.
- Prioritize admin-feature leakage, unauthorized stylesheet access, and user-controlled CSS abuse over stylistic quality issues.

- Find weaknesses and missing controls that materially affect the security properties of this topic.
- Prioritize issues that are exploitable, exposed to untrusted input, or central to the trust boundaries in scope.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the stylesheets, selector naming patterns, and user-controlled styling surfaces in scope.
2. Trace who can fetch, infer, or influence those styles and what application behavior or information they reveal.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they reveal privileged capability or enable visual deception and user-confusion attacks.

## Review Checklist

### Stylesheet Access Control and Segmentation
- Check whether role- or environment-specific stylesheets are protected and not fetchable by unauthorized users.
- Check whether CSS is segmented to avoid shipping privileged UI information to every user unnecessarily.
- Flag monolithic CSS that reveals internal features, admin actions, or hidden workflows to ordinary users.

- Check whether the primary controls for this topic are explicitly implemented and enforced at the right trust boundary.
- Check failure behavior, defaults, and alternate paths rather than only the intended flow.
- Flag places where documentation or assumptions replace visible technical enforcement.

### Selector Naming and Information Disclosure
- Check selectors, IDs, and class names for disclosure of sensitive actions, internal objects, or authorization states.
- Check build-time minification, namespacing, or CSS module strategies where disclosure risk matters.
- Flag descriptive selectors that materially help attackers enumerate capabilities or hidden functions.

- Check how untrusted input, third-party integrations, and cross-component boundaries affect this topic.
- Check whether surrounding systems can bypass or weaken the expected control model.
- Flag inconsistent enforcement across components, services, clients, or environments.

### User-Controlled Styling and Overlay Abuse
- Check whether user-generated content can inject or influence CSS, positioning, overlays, or z-index-driven deception.
- Check whether sanitization and CSP prevent abuse of inline or uploaded styling.
- Flag styling paths that can hide warnings, mimic trusted UI, or support clickjacking-like overlays.

- Check tests, logging, monitoring, and deployment practices relevant to this topic.
- Check whether operational handling preserves the intended security properties over time.
- Treat missing evidence about important controls as review gaps.

### Consistency and Monitoring

- Check whether CSS delivery and naming are consistent enough to avoid leaking more than intended across pages or roles.
- Check tests or review notes for unauthorized stylesheet access and user-generated styling abuse.
- Check whether frontend teams can detect accidental disclosure of sensitive selector semantics over time.

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

- Critical: CSS exposure or user-controlled styling materially enables high-impact deception or reveals sensitive privileged capability with direct attacker value.
- High: Unauthorized stylesheet access, descriptive selectors, or CSS injection materially improve attacker reconnaissance or UI abuse.
- Medium: Selector and isolation weaknesses increase exposure but need supporting conditions to become materially exploitable.
- Low: Naming and consistency improvements with stronger primary protections otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects stylesheet access, selector disclosure, user-controlled styling, or CSS consistency.
- Mention the exact stylesheet, selector family, or styling injection path involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
