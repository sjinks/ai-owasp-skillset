---
name: security-review-owasp-llm-prompt-injection-prevention
description: Review LLM Prompt Injection Prevention concerns, trust boundaries, and operational assumptions against OWASP LLM Prompt Injection Prevention guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP LLM Prompt Injection Prevention Security Review

Apply this skill to security reviews focused on LLM Prompt Injection Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on prompt injection, unsafe tool execution, context leakage, untrusted content ingestion, excessive agent autonomy, and weak model-operation guardrails. Prefer precise findings over broad advice.

## Scope

- agent, model, prompt, tool, retrieval, and plugin execution paths related to this topic
- trust boundaries between user input, retrieved content, tools, external services, and model outputs
- deployment, logging, governance, and runtime controls that determine what the system can access or do

## Review Goals

- Find paths where untrusted content can steer model behavior, trigger unsafe actions, or leak sensitive context.
- Prioritize direct abuse paths such as tool misuse, secret exposure, policy bypass, and unsafe autonomous execution.
- Produce findings with concrete evidence from prompts, code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify model inputs, retrieved data sources, tools, memory, and output consumers in scope.
2. Trace how untrusted content can influence prompts, tool calls, state, or downstream actions.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they permit prompt injection, tool abuse, data leakage, or unsafe automation.

## Review Checklist

### Prompt, Context, and Retrieval Boundaries

- Check whether untrusted content is clearly separated from trusted instructions and system policy.
- Check whether retrieved or user-supplied content can silently override role, policy, or safety assumptions.
- Flag weak context-isolation patterns that let one tenant, session, or document influence another.

### Tools, Actions, and Side Effects

- Check that tool invocation is constrained by allowlists, least privilege, and explicit policy.
- Flag agent flows that can perform sensitive actions from model output alone without strong confirmation or policy checks.
- Check external calls, connectors, and automations for data exfiltration and command-injection style abuse paths.

### Data Protection and Operations

- Check secret handling, memory retention, logs, traces, and training or evaluation data exposure.
- Check whether model outputs are validated before execution, storage, or high-trust rendering.
- Check incident response, monitoring, and abuse throttling for model-driven workflows.

### Topic Focus Areas

- Review prompt, context, tool, and model trust boundaries most affected by this topic.
- Review how untrusted input can influence model instructions, retrieved context, tool invocation, or downstream actions.
- Review least-privilege design, output validation, and human-approval gates relevant to this topic.
- Review logging, monitoring, and containment controls for prompt abuse, tool misuse, or model-side security failures.
- Review whether this topic is enforced consistently across development, evaluation, and production AI workflows.

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

- Critical: Prompt or tool abuse that enables direct data exfiltration, privileged action execution, or cross-tenant compromise.
- High: Reliable context leakage, policy bypass, unsafe connector access, or weak runtime controls with realistic abuse paths.
- Medium: Important isolation, validation, or monitoring gaps that increase risk but need additional conditions.
- Low: Hardening and governance gaps with stronger primary controls otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects prompts, retrieval, tools, memory, model outputs, or runtime policy.
- Mention the attacker-controlled input and the resulting unsafe action or disclosure path when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
