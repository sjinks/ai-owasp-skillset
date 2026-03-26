---
name: security-review-owasp-file-upload
description: Review file upload features and storage handling against OWASP file upload guidance. Use for validation, storage isolation, file processing, and upload authorization.
argument-hint: Optionally specify the upload flow, storage backend, or files to focus on
---

# OWASP File Upload Security Review

Apply this skill to security reviews focused on File Upload concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on remote code execution paths, malicious file delivery, overwrite and traversal risks, storage exposure, and missing upload authorization. Prefer precise findings over broad advice.

## Scope

Apply this skill to:

- upload endpoints and multipart handling
- extension, MIME type, and file signature validation
- filename generation and sanitization
- storage layout, webroot exposure, and object-store policies
- content scanning and document or archive processing
- upload and download authorization checks
- CSRF protection, size limits, and rate limiting

## Review Goals

- Find upload weaknesses that let attackers store, execute, replace, or distribute dangerous content.
- Prioritize exploitable paths such as executable uploads, extension bypasses, traversal, and missing access control.
- Produce findings with concrete evidence from code, configuration, tests, or observable behavior.

## Review Procedure

1. Identify all upload and retrieval paths.
   Include APIs, forms, admin tooling, document processing jobs, and file download endpoints.
2. Trace how uploaded content is validated, named, stored, and later served or processed.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
5. Prioritize findings by code-execution risk, data exposure risk, and ease of abuse.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for tracing validation, storage, and serving behavior end to end.

## Review Checklist

### Extension and Type Validation

- Check whether allowed file types are enforced with an allowlist rather than a blocklist.
- Check validation after decoding the filename and path components.
- Flag double-extension, null-byte, or parser-confusion bypass opportunities.
- Check whether MIME type validation is present but not treated as sufficient by itself.
- Check whether file signatures or magic bytes are validated for sensitive or dangerous file classes.

### Filename and Path Safety

- Check whether files are renamed to random identifiers or otherwise restricted to a narrow safe character set.
- Flag directory traversal characters, reserved names, hidden-file patterns, or overwrite risks.
- Check whether filename length limits are enforced.
- Check whether storage paths are derived from validated server-side state rather than raw user input.

### Storage and Serving

- Check whether uploaded files are stored outside the webroot or on a separate host or bucket.
- If stored in a web-accessible location, flag executable permissions or direct serving of dangerous content.
- Check whether public access is mediated through a mapped identifier rather than a raw storage path.
- Check whether object-store policies accidentally expose uploaded content.

### Authorization and Request Defenses

- Check whether upload requires authentication and the correct authorization level.
- Check whether download or retrieval re-validates authorization rather than assuming possession of a URL is enough.
- Check whether upload requests are protected against CSRF where relevant.
- Check whether upload and download endpoints have sensible rate limiting.

### Size, Archives, and Content Processing

- Check file size limits before expensive processing.
- Flag archive extraction without defenses against zip bombs or path traversal.
- Check whether documents and media are scanned, rewritten, sandboxed, or otherwise processed safely when business requirements justify it.
- Check whether uploaded files are processed with privileged tools or in unsafe execution contexts.

### Dangerous File Types

- Flag executable, script, or server-interpreted file types that can reach executable locations.
- Check office documents, PDFs, archives, and image processing flows for risky parsers or libraries.
- Check whether unsupported or unnecessary file classes are rejected.

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

- End with a short conclusion covering the dominant upload risk theme.

## Severity Guidance

- Critical: Upload-to-execution paths, unrestricted dangerous file upload, or missing authorization on sensitive upload or download flows.
- High: Extension bypasses, traversal or overwrite risks, dangerous webroot exposure, or weak validation that realistically enables exploitation.
- Medium: Important hardening gaps such as missing signature checks, weak size controls, or incomplete CSRF defenses with narrower impact.
- Low: Limited hardening gaps where layered protections still clearly prevent dangerous outcomes.

## Findings Format Rules

- Prefer root causes over enumerating every bypass variant separately.
- Distinguish between content-type validation and real content validation.
- Do not assume malware scanning exists unless it is visible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- Do not produce a long narrative summary before listing findings.
