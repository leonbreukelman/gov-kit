<!--
  SYNC IMPACT REPORT
  ==================
  Version change: (new) → 1.0.0
  
  Modified principles: N/A (initial ratification)
  
  Added sections:
    - Core Principles (5 principles)
    - Technology Stack (new section)
    - Development Workflow (new section)
    - Governance
  
  Removed sections: N/A
  
  Templates requiring updates:
    - .specify/templates/plan-template.md ✅ (compatible - generic structure)
    - .specify/templates/spec-template.md ✅ (compatible - generic structure)
    - .specify/templates/tasks-template.md ✅ (compatible - generic structure)
    - .specify/templates/agent-file-template.md ✅ (compatible - generic structure)
    - .specify/templates/checklist-template.md ✅ (compatible - generic structure)
  
  Follow-up TODOs: None
-->

# gov-kit Constitution

## Core Principles

### I. AI-First & Agentic Design

All features MUST be designed for agentic consumption before human convenience. This means:

- Every operation exposes a programmatic interface (CLI, API, or SDK) with structured I/O (JSON preferred, human-readable optional).
- Natural language interfaces and LLM tool definitions are first-class citizens—not afterthoughts.
- State and context MUST be externally inspectable and recoverable to support agent orchestration.
- Documentation MUST include agentic usage examples alongside human instructions.

**Rationale**: gov-kit exists to enable AI agents to manage repositories autonomously. Human usability follows from good agentic design, not the reverse.

### II. Library-First Architecture

Every feature starts as a standalone, independently testable Python library:

- Libraries MUST be self-contained with explicit dependencies declared in `pyproject.toml`.
- Each library MUST have a clear, singular purpose—no "util" or organizational-only modules.
- Libraries MUST expose functionality via CLI commands using standard argument parsing.
- All I/O follows text protocols: stdin/args → stdout, errors → stderr.

**Rationale**: Composability enables both human developers and AI agents to chain capabilities predictably.

### III. Test-First Development (NON-NEGOTIABLE)

TDD is mandatory for all production code:

- Tests MUST be written before implementation and approved before proceeding.
- Red-Green-Refactor cycle is strictly enforced: tests fail first, then pass, then optimize.
- Contract tests are required for all external interfaces (CLI, API).
- Integration tests are required for AWS service interactions and cross-module communication.

**Rationale**: Agentic systems require high reliability; untested code cannot be trusted by autonomous agents.

### IV. Infrastructure as Code & AWS Native

All infrastructure MUST be defined as code and AWS-native where applicable:

- AWS CDK (Python) is the preferred IaC tool; Terraform acceptable for multi-cloud needs.
- No manual AWS console changes in production—all changes flow through code.
- Secrets MUST use AWS Secrets Manager or SSM Parameter Store—never hardcoded.
- Lambda, Step Functions, and EventBridge are preferred for event-driven agentic workflows.

**Rationale**: Reproducibility and auditability are essential for governance tooling; manual changes are ungovernable.

### V. Simplicity & Observability

Start simple, stay observable, justify complexity:

- YAGNI: Do not implement features until explicitly needed.
- Structured logging (JSON) is mandatory for all operations; use Python `structlog` or equivalent.
- All long-running operations MUST emit progress indicators suitable for agent polling.
- Error messages MUST be actionable and include recovery suggestions.

**Rationale**: Agents cannot debug opaque failures; humans benefit from the same clarity.

## Technology Stack

**Runtime**: Python 3.12+ (strict type hints required via mypy or pyright)
**Package Management**: uv (mandatory; no pip/poetry/conda in CI/CD)
**Cloud Platform**: AWS (Lambda, Step Functions, DynamoDB, S3, EventBridge, CDK)
**Testing**: pytest with pytest-cov for coverage (≥80% line coverage required)
**Linting/Formatting**: ruff (replaces black, isort, flake8)
**Type Checking**: pyright (strict mode)
**Documentation**: Markdown files; API docs via docstrings + mkdocs

## Development Workflow

### Code Quality Gates

All PRs MUST pass before merge:

1. `uv run ruff check .` — zero violations
2. `uv run ruff format --check .` — formatting verified
3. `uv run pyright` — zero type errors in strict mode
4. `uv run pytest --cov=src --cov-fail-under=80` — tests pass with coverage threshold

### Branching Strategy

- `main` is always deployable
- Feature branches: `###-feature-name` (issue number prefix)
- All changes via PR with at least one approval

### Documentation Requirements

- Every new module MUST include docstrings following Google style
- CLI commands MUST include `--help` with usage examples
- Breaking changes MUST be documented in CHANGELOG.md

## Governance

This Constitution supersedes all other development practices in the repository.

### Amendment Process

1. Propose changes via PR to `.specify/memory/constitution.md`
2. Document rationale and impact assessment
3. Require explicit team approval
4. Update version per semantic versioning:
   - MAJOR: Principle removal or incompatible redefinition
   - MINOR: New principle or materially expanded guidance
   - PATCH: Clarifications, wording, non-semantic refinements
5. Propagate changes to dependent templates and documentation

### Compliance

- All PRs MUST verify compliance with active principles
- Constitution violations block merge
- Exceptions require documented justification and governance approval
- Use `.specify/` templates for runtime development guidance

**Version**: 1.0.0 | **Ratified**: 2025-12-07 | **Last Amended**: 2025-12-07
