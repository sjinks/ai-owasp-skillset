---
name: security-review-owasp-ruby-on-rails
description: Review Rails request handling, parameter binding, session and CSRF controls, and framework configuration against OWASP Rails guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Ruby on Rails Security Review

Apply this skill to security reviews focused on Ruby on Rails concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on unsafe parameter handling, weak controller and model boundaries, session and CSRF mistakes, unsafe rendering, file-handling risks, and stale Rails defaults or dependency posture. Prefer precise findings over broad advice.

## Scope

- Rails controllers, routes, strong parameters, models, views, jobs, and framework configuration reachable from user input
- authentication, session, CSRF, Active Record, file handling, rendering, and serialization patterns shaped by Rails defaults or custom overrides
- gem and framework update posture where security guidance depends on current defaults and patched components

## Review Goals

- Find Rails-specific weaknesses that let attacker input cross controller or model boundaries unsafely, bypass access control, or abuse framework conveniences.
- Prioritize strong-parameter failures, unsafe rendering or file handling, and session or CSRF mistakes over generic operational issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the Rails endpoints, controllers, models, background jobs, and rendering paths in scope.
2. Trace how request parameters, session state, uploaded files, and model changes move through Rails conventions and custom overrides.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they enable account compromise, unauthorized data access, or unsafe server-side behavior.

## Review Checklist

### Parameter Handling and Controller Boundaries

- Check `strong_parameters`, controller filtering, and route handling for unsafe mass assignment or hidden-field control.
- Check whether parameter-driven behavior reaches dangerous redirects, file paths, query fragments, or serializer options.
- Flag controllers that trust client state or skip explicit authorization because the route looks internal.

### Session, CSRF, and Rendering Safety

- Check session cookies, CSRF protections, and reauthentication for sensitive Rails actions and admin workflows.
- Check view rendering, template helpers, and content handling for XSS or unsafe HTML output patterns.
- Flag places where Rails security defaults were disabled or bypassed without strong replacement controls.

### Models, Files, and Background Work

- Check Active Record queries, callbacks, and associations for injection, authorization, or data ownership mistakes.
- Check file uploads, attachment handling, and background jobs for unsafe processing, path trust, or privilege confusion.
- Flag serialization and deserialization patterns that expose internal objects or trust unverified payloads.

### Framework Configuration and Update Posture

- Check Rails and gem versions against current supported security posture and known mitigation defaults.
- Check secret management, environment separation, and production-safe configuration.
- Check whether update or security-review processes actually cover framework-level security changes.

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

- Critical: Rails-specific weaknesses enable direct unauthorized access, unsafe file or code execution, or broad account compromise.
- High: Strong-parameter, session, CSRF, rendering, or model-boundary flaws materially improve attacker success.
- Medium: Configuration, update, or background-processing gaps increase exposure but need additional conditions.
- Low: Hardening and observability issues with strong primary Rails controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects parameter handling, sessions and CSRF, models and files, or framework configuration.
- Mention the exact controller, model, helper, job, or Rails setting involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
