---
name: security-review-owasp-c-based-toolchain-hardening
description: Review compiler, linker, loader, and build hardening settings for native code against OWASP C-based toolchain hardening guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP C-based Toolchain Hardening Security Review

Apply this skill to security reviews focused on C-based Toolchain Hardening concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on missing compile-time and link-time hardening, inconsistent build profiles, unsafe exceptions for native modules, and packaging choices that weaken exploit resistance. Prefer precise findings over broad advice.

## Scope

- compiler, linker, loader, and archive settings for C or C++ components and their release artifacts
- build system configuration, hardening flags, optimization profiles, and debug or compatibility exceptions across environments
- native dependencies, third-party libraries, and verification steps that determine whether hardening is actually present in shipped binaries

## Review Goals

- Find native build configurations that disable or silently omit exploit-mitigation features such as stack protection, RELRO, PIE, or Fortify.
- Prioritize missing hardening in production builds and privileged binaries over minor build hygiene issues.
- Produce findings with concrete evidence from build files, compiler flags, CI configuration, packaged binaries, documentation, or observable behavior.

## Review Procedure

1. Identify the native components, build systems, compiler toolchains, and artifact types in scope.
2. Trace which hardening flags and verification steps apply in development, CI, release, and distribution builds.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they materially weaken exploit resistance for reachable native code.

## Review Checklist

### Compiler and Linker Hardening

- Check for stack canaries, PIE, RELRO, NX, Fortify, control-flow protections, and equivalent hardening settings appropriate to the toolchain.
- Check whether hardening is enabled by default in release builds rather than opt-in or selectively disabled.
- Flag linker or optimization settings that silently negate expected protections.

### Build Profile Consistency

- Check build-system definitions for environment-specific drift between debug, test, and production artifacts.
- Check whether exceptions for compatibility or performance are narrowly scoped and documented.
- Flag paths where third-party or legacy modules bypass the intended hardening baseline.

### Verification and Artifact Assurance

- Check CI or release verification that inspects produced binaries rather than trusting build flags alone.
- Check packaging and distribution steps for stripped or rebuilt artifacts that may lose protections.
- Flag lack of repeatable verification for shipped binaries and native libraries.

### Topic Focus Areas

- Review compiler, linker, loader, and runtime hardening settings that materially affect memory-corruption resistance in shipped binaries.
- Review whether protections such as PIE, RELRO, stack canaries, non-executable memory, control-flow protections, and fortified builds are enabled where supported.
- Review build-system paths, feature flags, and packaging steps that could silently drop hardening options between environments or artifacts.
- Review third-party, legacy, or compatibility-driven exceptions that weaken the native-code hardening baseline.
- Review verification steps that confirm final binaries retain the intended hardening properties instead of trusting build definitions alone.

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

- Critical: Reachable native components ship without core exploit mitigations, materially enabling memory-corruption exploitation.
- High: Important hardening flags or verification steps are inconsistent, disabled, or bypassed for production artifacts.
- Medium: Build-profile drift or undocumented exceptions weaken assurance but do not prove a broad mitigation failure alone.
- Low: Observability and process gaps with strong hardening otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects compile-time hardening, link-time hardening, build consistency, or artifact verification.
- Mention the exact flag, toolchain setting, or artifact verification gap involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
