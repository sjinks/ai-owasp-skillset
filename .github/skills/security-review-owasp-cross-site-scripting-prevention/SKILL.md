---
name: security-review-owasp-cross-site-scripting-prevention
description: Review rendering code and client-side sinks against OWASP XSS prevention guidance. Use for context-sensitive output encoding, sanitization, safe sinks, and framework escape hatches.
argument-hint: Optionally specify the rendering flow, framework, or files to focus on
---

# OWASP Cross Site Scripting Prevention Security Review

Apply this skill to security reviews focused on Cross Site Scripting Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on untrusted data entering dangerous output contexts, framework escape hatches, unsafe DOM sinks, missing sanitization, and incorrect encoding strategies.

## Scope

Apply this skill to:

- templates, server-rendered views, and component rendering
- framework escape hatches such as raw HTML rendering features
- DOM updates using HTML, script, style, URL, or event-handler sinks
- user-authored HTML rendering and sanitization logic
- client-side code using location, referrer, cookies, storage, or API data as render inputs
- context-specific encoding and content-type handling

## Review Goals

- Find XSS paths where untrusted data can execute as script or reach dangerous browser contexts.
- Prioritize raw HTML insertion, script-context injection, unsafe DOM sinks, and explicit bypasses of framework protections.
- Produce findings with concrete evidence from code, templates, or observable behavior.

## Review Procedure

1. Identify every place untrusted data is rendered or written into the DOM.
   Include server templates, client rendering code, markdown or HTML previews, and rich-text features.
2. Trace the data into its final output context.
   Distinguish HTML body, attribute, JavaScript, CSS, and URL contexts.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
5. Prioritize findings by execution reliability, attacker control, and whether the issue is stored, reflected, or DOM-based.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as exhaustive framework guidance.

## Review Checklist

### Framework Protections and Escape Hatches

- Check whether the framework auto-escapes output by default in the paths under review.
- Flag raw HTML rendering features such as `dangerouslySetInnerHTML`, `unsafeHTML`, or equivalent bypass APIs unless sanitized input is clearly enforced.
- Flag trust-bypass APIs such as Angular security bypass helpers when fed untrusted content.

### Output Context and Encoding

- Check whether untrusted data in HTML body context is HTML-encoded.
- Check whether untrusted data in HTML attributes is handled with attribute-safe encoding and safe attribute selection.
- Check whether untrusted data is ever inserted into JavaScript contexts, and if so, whether the design avoids it or applies correct protections.
- Check whether untrusted data reaches CSS or style contexts.
- Check whether user-controlled URLs are validated and encoded before insertion into `href`, `src`, or similar sinks.
- Check JSON responses used by browsers for the correct content type where relevant.

### Sanitization and User-Authored HTML

- Check whether HTML sanitization is applied when user-authored HTML must be rendered.
- Flag post-sanitization mutation or later DOM rewriting that can reintroduce unsafe markup.
- Check whether sanitization libraries are actively maintained.

### Safe and Unsafe DOM Sinks

- Prefer safe sinks such as text-only DOM APIs over `innerHTML` or equivalent HTML-parsing sinks.
- Flag string-based `eval`, `setTimeout`, `setInterval`, or dynamic event-handler construction.
- Check whether untrusted data from location, referrer, storage, or messages is written into unsafe DOM sinks.

### Anti-Patterns and Dangerous Contexts

- Flag reliance on CSP as the primary defense instead of correct encoding and sanitization.
- Flag centralized interceptor-style encoding that ignores output context.
- Flag dangerous contexts such as script blocks, event-handler attributes, comments, and raw style attributes for untrusted data.

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

- End with a short conclusion covering the dominant XSS risk theme.

## Severity Guidance

- Critical: Reliable script execution via raw HTML sinks, script context injection, or explicit framework protection bypasses on attacker-controlled data.
- High: DOM-based XSS, unsafe URL or event-handler injection, or stored or reflected injection with realistic execution.
- Medium: Important encoding or sanitization gaps with limited exploitability or additional preconditions.
- Low: Hardening gaps where strong framework defaults and safe sinks clearly reduce exploitability.

## Findings Format Rules

- Always mention the output context or sink that makes the issue exploitable.
- Distinguish sanitized HTML rendering from plain text rendering.
- Do not treat CSP as proof that an XSS issue is mitigated.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- Do not produce a long narrative summary before listing findings.
