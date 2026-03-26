---
name: security-review-owasp-xml-external-entity-prevention
description: Review XML parser hardening, DTD handling, entity resolution, and parser-specific XXE defenses against OWASP XXE guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP XML External Entity Prevention Security Review

Apply this skill to security reviews focused on XML External Entity Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on DTD processing, external entity resolution, entity expansion, parser-version safety, XInclude handling, and unsafe XML pipelines that can lead to SSRF, file disclosure, or parser-based denial of service. Prefer precise findings over broad advice.

## Scope

- XML parser initialization and hardening across languages and frameworks
- DTD, external entity, parameter entity, XInclude, and entity-expansion controls
- transformers, validators, schema loaders, unmarshallers, and other downstream XML consumers
- parser and library versions that are only safe after specific releases or configuration changes
- XXE-to-SSRF and blind XXE paths triggered by network fetches during parsing or validation

## Review Goals

- Find parser configurations that permit external entity resolution, file disclosure, SSRF, or entity-expansion denial of service.
- Prioritize DTD and external-entity enablement, unsafe parser defaults, and cross-parser unsafe pipelines over lower-impact version hygiene issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every place untrusted XML can enter the system, including API requests, file uploads, messaging, and third-party integrations.
2. Trace the XML through parser initialization, validation, transformation, unmarshalling, and any downstream consumers.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they permit SSRF, local file disclosure, blind XXE, or entity-expansion denial of service.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for parser-by-parser hardening review.

## Review Checklist

### Parser Initialization and Core Hardening

- Check whether DTD processing is disabled where untrusted XML is parsed.
- Check whether external general entities, external parameter entities, and external DTD loading are explicitly disabled.
- Check secure-processing and entity-expansion controls to prevent Billion Laughs style denial of service.
- Check whether XInclude is disabled where untrusted XML is handled.

### Parser-Specific Safety and Versioning

- Check parser and framework versions for known safe-by-default thresholds, but do not rely on version alone when explicit hardening is still needed.
- Check language-specific parser flags and builders for safe settings instead of assuming defaults.
- Flag deprecated or fundamentally unsafe XML helpers such as XML decoders that should not process untrusted input.

### Downstream XML Pipelines

- Check transformers, validators, schema factories, stream readers, JAXB-like unmarshallers, and XPath consumers for safe upstream parser input.
- Check whether schema validation or transformation can fetch external DTDs, schemas, or stylesheets.
- Flag pipelines where one hardened parser hands off to a later unsafe consumer.

### SSRF and Blind XXE Paths

- Check whether XXE could trigger outbound requests, metadata access, or DNS probes even when direct response data is not returned.
- Check whether network egress controls reduce the impact of parser mistakes.
- Check whether parser misconfiguration could be chained with SSRF-sensitive endpoints or internal services.

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

- Critical: DTD and external-entity enablement that permits SSRF, local file disclosure, or blind XXE against reachable untrusted XML paths.
- High: Unsafe downstream XML consumers, external schema or stylesheet fetching, or parser configurations that permit strong XXE exploitation paths.
- Medium: Entity-expansion, version, or XInclude gaps that increase risk but do not clearly prove file or network access.
- Low: Hardening and observability gaps with strong parser restrictions otherwise in place.

## Findings Format Rules

- State whether the weakness affects parser initialization, downstream consumers, or external fetch behavior.
- Mention the specific parser, builder, or factory when possible.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
