<!--
Sync Impact Report:
- Version change: 1.1.0 -> 1.1.1
- List of modified principles:
  - UI/UX Standards (Clarified: Mobile-First + Desktop Optimization)
- Added sections: N/A
- Removed sections: N/A
- Templates requiring updates:
  - ✅ updated: .specify/templates/plan-template.md
  - ✅ updated: .specify/templates/spec-template.md
  - ✅ updated: .specify/templates/tasks-template.md
- Follow-up TODOs: None
-->

# Facturador JB Tech Constitution

## Core Principles

### I. Architecture & Stack
Facturador JB Tech is built using a modern, scalable stack and a robust architectural pattern to ensure long-term maintainability.
- **Backend**: Java LTS (Current Long Term Support version).
- **Frontend**: React.
- **Architecture**: Strict adherence to **Clean Architecture** and **SOLID** principles.
- **Dependency Management**: Minimalist approach to external libraries. Every third-party dependency must be strictly justified to avoid bloat and security risks.

### II. Resilience & Fault Tolerance
The system must remain operational and degrade gracefully when external components fail.
- **Patterns**: Mandatory implementation of **Circuit Breaker** and **Rate Limit** for all interactions with external services (e.g., AFIP API).
- **Retry Policy**: Default policy of **3 retries** with **Exponential Backoff** for all transient failures.

### III. Exception Management
Exceptions are treated as first-class citizens in our logic flow, ensuring system stability and security.
- **Handling**: Catch exceptions only if they are being explicitly managed or transformed into a domain-specific context.
- **Anti-pattern**: Avoid the 'catch-log-rethrow' pattern. Logs should happen where the exception is finally handled or at the boundary.
- **Security**: Prevent internal stacktraces from ever reaching the client/frontend. All public errors must be mapped to safe, user-friendly messages.

### IV. Testing Discipline (TDD)
Quality is built-in, not added later.
- **Approach**: **TDD is mandatory**. No implementation code should be written without a failing test first.
- **Unit Testing**: Tests must be significant, non-redundant, and achieve >80% coverage.
- **Integration & E2E**: Prioritize functional and End-to-End tests that validate the complete user flow over excessive low-level unit tests.

### V. Code Quality & Observability
Code must be easy to read, understand, and debug.
- **Documentation**: Prioritize self-documenting code (meaningful names, clear structure). Javadoc is reserved for explaining the *purpose* and *return values* of public APIs.
- **Logging**: Focused on troubleshooting. Every log entry must provide technical context (correlation IDs, state). 
- **Privacy**: Strictly prohibit logging PII (Personally Identifiable Information) or sensitive data (credentials, secrets).

### VI. UI/UX Standards
We build tools that empower users through simplicity and speed across all devices.
- **Unified Frontend Strategy**: Maintain a single React codebase using **Responsive Web Design (RWD)** and **PWA** capabilities. This ensures consistency, avoids logic duplication, and simplifies maintenance.
- **Mobile-First Excellence**: Interfaces must be designed starting from the mobile experience (content prioritization). Mobile users should have access to 100% of the core functionality with a premium, native-like feel.
- **Desktop Power-User Efficiency**: The desktop experience must leverage extra screen real estate for higher data density, advanced filtering, and keyboard-driven productivity required for intensive administrative work.
- **Efficiency**: Maximize user productivity by minimizing the number of clicks required to complete any primary task.

### VII. Security & Delivery
Safety and consistency in how we build and deploy.
- **Compliance**: Follow **OWASP** top 10 security practices.
- **Secrets**: All secrets (API keys, DB credentials) must be managed strictly via environment variables. Never hardcode or commit secrets.
- **Commits**: Use **Conventional Commits** for all changes. Each commit must be atomic and represent a single logical change.

## Governance
This constitution is the supreme guide for development within the Facturador JB Tech project.

- **Compliance**: All Pull Requests must be reviewed against these principles. Violations require explicit justification in a "Complexity Tracking" section of the implementation plan.
- **Amendments**: Changes to these principles require a version bump and updated documentation across all templates.
- **Versioning**: Follow Semantic Versioning for the constitution itself.

**Version**: 1.1.1 | **Ratified**: 2026-05-11 | **Last Amended**: 2026-05-16
