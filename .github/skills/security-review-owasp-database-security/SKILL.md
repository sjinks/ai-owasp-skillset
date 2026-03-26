---
name: security-review-owasp-database-security
description: Review database credential handling, least privilege, transport protection, network isolation, and hardening against OWASP database security guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP Database Security Review

Apply this skill to security reviews focused on Database Security concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on database trust boundaries, overly privileged accounts, insecure credential storage, weak transport protections, and exposed administration or network surfaces. Prefer precise findings over broad advice.

## Scope
- application-to-database connectivity, database account permissions, credential storage, and network reachability
- TLS, certificate validation, database listener binding, firewall policy, and segmentation around SQL servers
- OS and database baseline hardening, dangerous stored procedures or features, and backup protection

- code paths, configuration, infrastructure, tests, and workflows related to this topic
- trust boundaries, dependencies, integrations, and operational controls influenced by this topic
- documentation or security assumptions that materially affect how the controls are implemented

## Review Goals
- Find weaknesses that let attackers reach, authenticate to, tamper with, or laterally abuse the database tier more easily than intended.
- Prioritize exposed databases, admin-level app accounts, and plaintext or hardcoded credentials over lower-impact operational cleanup.

- Find weaknesses and missing controls that materially affect the security properties of this topic.
- Prioritize issues that are exploitable, exposed to untrusted input, or central to the trust boundaries in scope.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify the databases, application accounts, admin paths, and network boundaries in scope.
2. Trace how credentials are provisioned, how connections are protected, and which privileges the application actually uses.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns. If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by whether they expose the database tier directly or materially increase post-compromise impact.

## Review Checklist

### Credential and Access Management
- Check that database credentials are not hardcoded, checked into source, or stored unencrypted in deployable config.
- Check that each application or service uses a dedicated account with host restrictions and only needed database access.
- Flag shared, static, or privileged credentials that widen blast radius across systems.

- Check whether the primary controls for this topic are explicitly implemented and enforced at the right trust boundary.
- Check failure behavior, defaults, and alternate paths rather than only the intended flow.
- Flag places where documentation or assumptions replace visible technical enforcement.

### Least Privilege and Dangerous Features
- Check granted operations against actual application needs and flag `CREATE`, `DROP`, `ALTER`, ownership, or OS-level extensions where unnecessary.
- Check privileged built-in accounts, dangerous stored procedures, and extra features such as shell execution or sample databases.
- Flag application identities that can administer schema or escape into server-side execution.

- Check how untrusted input, third-party integrations, and cross-component boundaries affect this topic.
- Check whether surrounding systems can bypass or weaken the expected control model.
- Flag inconsistent enforcement across components, services, clients, or environments.

### Transport and Network Isolation
- Check TLS version, cipher support, certificate validation, and host verification for database connections.
- Check listener binding, internal-only exposure, firewall rules, and security-zone separation between app and database tiers.
- Flag databases reachable from public or overly broad internal networks.

- Check tests, logging, monitoring, and deployment practices relevant to this topic.
- Check whether operational handling preserves the intended security properties over time.
- Treat missing evidence about important controls as review gaps.

### OS, Backup, and Patch Hardening

- Check service account privileges, OS baseline hardening, and patch posture for database hosts.
- Check backup encryption, access control, and log or transaction-disk handling for sensitive data at rest.
- Check verification that deployed database settings match documented hardening expectations.

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

- Critical: Databases are exposed, weakly authenticated, or reachable with overly privileged application accounts.
- High: Credential handling, TLS validation, or privilege boundaries materially increase attacker success or impact.
- Medium: Hardening, backup, or segmentation gaps increase exposure but need additional attacker conditions.
- Low: Operational and baseline-hardening issues with stronger primary protections otherwise in place.

## Findings Format Rules

- Tie each finding to visible evidence from code, configuration, tests, documentation, or behavior.
- Prefer root causes over listing multiple symptoms separately.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.
- State whether the weakness affects credentials, privileges, transport, network isolation, or host hardening.
- Mention the exact database account, listener, procedure, or configuration involved when possible.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
