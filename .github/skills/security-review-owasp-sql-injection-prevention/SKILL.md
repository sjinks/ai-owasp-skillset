---
name: security-review-owasp-sql-injection-prevention
description: Review query construction, parameterization, stored procedures, and database privilege boundaries against OWASP SQL injection guidance.
argument-hint: Optionally specify the flow, component, or files to focus on
---

# OWASP SQL Injection Prevention Security Review

Apply this skill to security reviews focused on SQL Injection Prevention concerns across code, configuration, architecture, and operational controls.

## Role

Act as a security reviewer. Focus on unsafe query construction, missing parameterization, risky stored procedures, unvalidated structural query elements, and over-privileged database accounts. Prefer precise findings over broad advice.

## Scope

- query builders, repositories, ORM raw-query paths, and direct database access code
- stored procedures, ad hoc SQL execution, reporting queries, migrations, and database utilities
- structural query inputs such as table names, column names, sort order, and pagination controls
- database account privileges, row or view-based restriction patterns, and error exposure behavior
- second-order injection paths where stored data later re-enters dynamic SQL

## Review Goals

- Find every realistic path where untrusted input can alter SQL structure or meaning.
- Prioritize direct concatenation, dynamic SQL in stored procedures, and high-privilege database access over weaker hygiene issues.
- Produce findings with concrete evidence from code, configuration, tests, documentation, or observable behavior.

## Review Procedure

1. Identify every path that assembles, executes, or influences SQL, including application code, ORM escape hatches, and stored procedures.
2. Trace all user-controlled or externally sourced values into query text, bound parameters, and structural query elements.
3. Validate each relevant area against the checklist below.
4. Separate confirmed weaknesses from unknowns.
   If a control may exist elsewhere but is not visible, report that as a review gap rather than as a confirmed vulnerability.
5. Prioritize findings by exploitability, reachable privilege level, and whether the issue affects values, structure, or both.

## Reference Material

- If short code examples would help distinguish safe and unsafe patterns, consult `reference/EXAMPLES.md` in this skill directory.
- Use the examples as recognition aids, not as a substitute for reviewing every sink, stored procedure, and privilege boundary.

## Review Checklist

### Query Construction and Parameterization

- Flag string concatenation, interpolation, formatting, or template expansion that incorporates untrusted data into SQL.
- Check that values in WHERE, HAVING, JOIN predicates, INSERT, UPDATE, and DELETE statements use prepared statements or equivalent parameter binding.
- Check whether ORMs or query builders are used safely, and flag raw SQL escape hatches that bypass parameterization.

### Stored Procedures and Dynamic SQL

- Check stored procedures for dynamic SQL assembled from user input.
- Flag `EXEC`, `EXECUTE`, `sp_executesql`, or similar dynamic execution patterns when inputs are not strictly constrained.
- Check that stored procedure calls from application code still bind parameters safely.
- Treat escaping-based defenses alone as insufficient unless the user input cannot influence SQL structure.

### Structural Query Elements

- Check table names, column names, sort direction, and other non-bindable query elements for strict allow-list mapping.
- Flag direct user control over ORDER BY, LIMIT, OFFSET, table, or column expressions.
- Check type coercion for numeric and boolean structural inputs before they influence SQL.

### Least Privilege and Blast Radius

- Check whether the application database account has only the minimum privileges it needs.
- Flag use of administrative, owner, or overly broad write privileges in ordinary application paths.
- Check whether views or role separation reduce blast radius for read-heavy or limited-function paths.

### Second-Order Injection and Error Handling

- Check whether stored user input later re-enters dynamic SQL in background jobs, admin tools, or reporting paths.
- Check whether database errors leak schema details or query fragments useful for exploitation.
- Check whether logs or monitoring can reveal attempted injection without exposing sensitive query details.

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

- Critical: Direct user-controlled SQL construction with meaningful data access or modification, especially under high-privilege database accounts.
- High: Dynamic SQL in stored procedures, structural query element control, or second-order injection with realistic reachability.
- Medium: Important allow-list, least-privilege, or error-handling gaps that increase exploitability but do not independently prove injection.
- Low: Secondary hardening gaps when parameterization and privilege boundaries are otherwise strong.

## Findings Format Rules

- State whether the weakness affects query values, SQL structure, stored procedures, or privilege boundaries.
- Mention the exact sink or execution path when possible.
- Distinguish confirmed weaknesses from review gaps where evidence is missing.

## Review Constraints

- Do not invent controls that are not visible in code, configuration, tests, or documentation.
- Do not produce a long narrative summary before listing findings.
