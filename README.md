# AI OWASP Skillset

This repository packages OWASP-derived VS Code Copilot Chat skills for security reviews.

The skills are designed to help reviewers inspect code, configuration, architecture, and operational controls against OWASP guidance without depending on the original cheat sheet files at review time.

## What Is Included

- 110 active security review skills under `.github/skills/`
- topic-specialized and category-specialized review playbooks
- 26 skills with optional `reference/EXAMPLES.md` files for compact safe and unsafe code patterns
- an index at `.github/skills/INDEX.md` describing skill coverage and specialization level

## Repository Layout

```text
.github/
  skills/
    INDEX.md
    security-review-owasp-<topic>/
      SKILL.md
      reference/
        EXAMPLES.md   # optional, only for example-heavy topics
skills -> .github/skills
```

Notes:

- `.github/skills/` is the canonical skill location used by VS Code Copilot Chat.
- `skills/` is a symlink to the same directory for convenience and compatibility.

## Skill Format

Each skill directory contains a `SKILL.md` file with:

- YAML frontmatter for skill metadata
- a security review role and scope definition
- review goals and a step-by-step review procedure
- a checklist tailored to the OWASP topic
- an output contract that standardizes findings
- severity guidance and review constraints

Some skills also include `reference/EXAMPLES.md` with short code or configuration snippets that help distinguish safe, unsafe, or incomplete implementations.

## Using The Skills In VS Code

1. Clone this repository.
2. Open it in VS Code with GitHub Copilot Chat enabled.
3. Ask Copilot to perform a security review against a topic covered by one of the skills.

Examples:

- `Review this authentication flow with the security-review-owasp-authentication skill.`
- `Check these GraphQL resolvers using the security-review-owasp-graphql skill.`
- `Review this webhook receiver with the security-review-owasp-webhook-security-guidelines skill.`

You can also browse `.github/skills/INDEX.md` to find the closest topic before starting a review.

## Reusing This Skill Pack

If you want these skills available inside another repository, copy or vendor the `.github/skills/` directory into that repository. The skills are self-contained and do not require the original OWASP markdown sources to function.

## Project Goal

The goal of this repository is not to mirror OWASP cheat sheets verbatim. Instead, it turns OWASP guidance into practical review playbooks optimized for Copilot-assisted security review workflows.

## Current Status

- active skills: 110
- topic-specialized: 38
- category-specialized: 72
- baseline skills: 0
- reference-backed skills: 26

See `.github/skills/INDEX.md` for the current inventory.