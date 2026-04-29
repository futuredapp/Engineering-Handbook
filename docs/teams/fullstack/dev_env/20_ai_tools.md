# AI-Assisted Development

AI tools have become an integral part of modern software development. This guide covers how we use them effectively and safely.

## Tools We Use

### Code Assistants

- **GitHub Copilot** — Inline code suggestions in the IDE. Useful for boilerplate, repetitive patterns, and exploring unfamiliar APIs.
- **Claude Code** — CLI-based AI assistant for larger tasks: refactoring, writing tests, exploring codebases, generating code across multiple files.
- **Cursor** — VS Code fork with built-in AI features, including multi-file editing and codebase-aware completions.

### Code Review

- **CodeRabbit** — Automated PR analysis that flags potential issues, suggests improvements, and provides summaries.
- **GitHub Copilot in PRs** — Summarizes changes and suggests improvements directly in pull requests.

### Documentation & Communication

- AI tools can help draft documentation, README files, and commit messages — but always review the output for accuracy.

## Guidelines for Effective Use

### When AI Helps Most

- **Boilerplate code** — CRUD endpoints, DTOs, entity definitions, test scaffolding
- **Exploring unfamiliar APIs** — "How do I use X in NestJS?" is faster to ask an AI than to browse docs
- **Writing tests** — AI is good at generating test cases, especially edge cases you might miss
- **Refactoring** — Renaming, restructuring, converting between patterns
- **Code review** — Catching common issues, suggesting improvements

### When to Be Careful

- **Business logic** — AI doesn't know your domain. Verify that generated logic actually matches the requirements.
- **Security-sensitive code** — Authentication, authorization, encryption. Always review carefully.
- **Database migrations** — AI-generated migrations can be subtly wrong. Test against real data.
- **Configuration** — CI/CD pipelines, Terraform, Docker configs. A small mistake can break production.

### General Principles

1. **AI suggests, you decide** — Treat AI output as a first draft, not final code
2. **Understand what you commit** — If you can't explain the code, don't commit it
3. **Review as if a junior wrote it** — AI produces plausible-looking code that can have subtle bugs
4. **Test AI-generated code** — It compiles doesn't mean it works correctly
5. **Don't disable your brain** — AI accelerates your work, it doesn't replace your judgment

## Security Rules

!!! warning "Critical Security Rules"

    - **Never paste secrets** (API keys, tokens, passwords, private keys) into AI prompts
    - **Never paste customer data** or PII into AI tools
    - **Be cautious with proprietary code** — check your AI tool's data retention policy
    - **Don't trust AI for security decisions** — it may suggest insecure patterns that look correct

### What's Safe to Share with AI

- Open-source code and public documentation
- Generic code patterns and architecture questions
- Error messages and stack traces (after removing secrets)
- Code from your own repositories (if using tools with data privacy guarantees like Copilot, Claude Code)

### What's NOT Safe to Share

- Environment variables, API keys, tokens
- Customer data or production database contents (PII — personally identifiable information)
- Internal infrastructure details (IP addresses, server configurations)
- Credentials or authentication tokens

## AI in Code Review

AI code review tools (CodeRabbit, Copilot) are part of our PR workflow, but they supplement — not replace — human review.

**What AI review catches well:**

- Common anti-patterns and code smells
- Missing error handling
- Inconsistent naming or formatting
- Potential performance issues
- Security basics (SQL injection, XSS)

**What requires human review:**

- Business logic correctness
- Architectural fit (does this belong here?)
- Context-specific decisions
- Edge cases that depend on domain knowledge

## Beyond Code Assistance

We actively experiment with AI in adjacent parts of the development workflow — test scaffolding, design handoff, QA, and project management. The team's current experiments and their status are tracked internally; this guide intentionally focuses on the practices that have stabilized, since experimental tooling shifts faster than we'd want this page to.

## Keeping Up

The AI tooling landscape evolves rapidly. We encourage team members to:

- Experiment with new tools and share findings
- Discuss useful AI workflows in team meetings
- Update this guide as practices evolve
