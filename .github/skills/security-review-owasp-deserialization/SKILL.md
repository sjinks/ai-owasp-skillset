---
name: security-review-owasp-deserialization
description: Review deserialization code and library configuration against OWASP deserialization guidance. Use for unsafe native formats, polymorphic type handling, and gadget-chain exposure.
argument-hint: Optionally specify the language, library, or files to focus on
---

# OWASP Deserialization Security Review

Apply this skill to security reviews focused on Deserialization concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on remote code execution risk, gadget-chain exposure, unsafe native deserialization, arbitrary type control, and insecure library configuration. Treat untrusted native deserialization as unsafe by default.

## Scope

Apply this skill to:

- native deserialization APIs such as Java object streams, Python pickle, PHP unserialize, and .NET binary serializers
- third-party serialization libraries and their security settings
- request handlers, queue consumers, file loaders, and database reads that deserialize untrusted data
- polymorphic type resolution and type-name handling
- custom deserialization hooks and class filters
- signed versus unsigned serialized payload handling

## Review Goals

- Find deserialization paths that allow attacker-controlled object creation or gadget execution.
- Prioritize remote code execution, arbitrary type materialization, and unsafe library defaults over lower-impact hygiene issues.
- Produce findings with concrete evidence from code, dependency usage, configuration, or observable behavior.

## Review Procedure

1. Identify every place serialized data enters the system.
   Include HTTP bodies, cookies, hidden fields, message queues, files, caches, and database-sourced blobs.
2. Trace how the data is parsed and which classes or types can be materialized.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
5. Prioritize findings by code-execution potential, type-control breadth, and exposure of the input source.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for library-version and type-resolution review.

## Review Checklist

### Native Deserialization Use

- Flag native deserialization of untrusted data unless there is clear evidence of strong class allowlisting or equivalent hardening.
- Check Java object stream usage for hardened class resolution, allowlists, or rejection of unexpected classes.
- Flag XMLDecoder and similar dangerous APIs on untrusted input.
- Check Python for unsafe `pickle` usage and unsafe YAML loaders.
- Check PHP for `unserialize` on untrusted data.
- Check .NET for unsafe serializers such as `BinaryFormatter`.

### Arbitrary Type Control

- Check whether user input, database state, or message metadata can steer type selection.
- Flag polymorphic deserialization features that accept attacker-controlled type names.
- Check whether libraries disable unsafe autotype or type-name handling by default and in practice.

### Library and Version Hardening

- Check whether serialization libraries are on versions consistent with the cheat sheet guidance.
- Check whether library safe modes, allowlists, or class registration features are enabled where required.
- Flag legacy or deprecated serializers that are known to be dangerous even when convenient.

### Custom Hooks and Defensive Controls

- Check custom deserializers, `readObject`-style hooks, or equivalent for defensive rejection of unsafe classes.
- Check whether signed payloads are validated before deserialization when native formats must be used.
- Check whether sensitive fields are excluded from serialization when the language supports that distinction.

### Safer Alternatives

- Check whether the design could use simpler data-only formats such as JSON instead of native object serialization.
- If XML is used, check that XML parsing is separately hardened against XML parser abuse.
- Check whether data-transfer objects are used instead of arbitrary object graphs.

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

- End with a short conclusion covering the dominant deserialization risk theme.

## Severity Guidance

- Critical: Untrusted native deserialization without strong type restrictions, or realistic gadget-chain execution paths.
- High: Unsafe polymorphic deserialization, dangerous serializer configuration, or exposed untrusted inputs feeding sensitive deserializers.
- Medium: Important hardening gaps, outdated libraries, or missing defensive hooks without direct proof of exploitation.
- Low: Limited serialization hygiene issues with strong compensating controls clearly in place.

## Findings Format Rules

- Treat unsafe native deserialization as insecure by default unless the implementation proves otherwise.
- Distinguish confirmed type restrictions from assumptions based on framework defaults.
- Mention the specific library or API involved whenever possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- Do not produce a long narrative summary before listing findings.
