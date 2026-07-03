# Folder Structure

### 14.1 Purpose

This document defines the implementation-ready folder structure for the GitHub Repository Optimizer Agent.

The structure is designed to support:

* Google ADK multi-agent orchestration.
* MCP-based GitHub integration.
* Reusable agent skills.
* Security guardrails.
* Structured schemas.
* Streamlit user interface.
* Automated testing and evaluation.
* Clear separation between application logic and project documentation.

The MVP uses a modular monolith architecture. This keeps the project simple enough for the capstone while preserving clean boundaries for future expansion.

---

# 14.2 Recommended Repository Structure

```text
github-repository-optimizer-agent/
│
├── app/
│   ├── __init__.py
│   ├── agent.py
│   ├── main.py
│   │
│   ├── agents/
│   │   ├── __init__.py
│   │   ├── coordinator.py
│   │   ├── repository_structure.py
│   │   ├── documentation.py
│   │   ├── quality.py
│   │   ├── security.py
│   │   ├── discoverability.py
│   │   └── portfolio.py
│   │
│   ├── skills/
│   │   ├── __init__.py
│   │   ├── repository_intake.py
│   │   ├── repository_classification.py
│   │   ├── technology_detection.py
│   │   ├── file_prioritization.py
│   │   ├── documentation_coverage.py
│   │   ├── engineering_hygiene.py
│   │   ├── security_hygiene.py
│   │   ├── discoverability.py
│   │   ├── portfolio_readiness.py
│   │   ├── recommendation_normalization.py
│   │   ├── prioritization.py
│   │   ├── scoring.py
│   │   ├── redaction.py
│   │   ├── draft_generation.py
│   │   └── limitations.py
│   │
│   ├── tools/
│   │   ├── __init__.py
│   │   ├── github_mcp_adapter.py
│   │   ├── repository_tools.py
│   │   ├── metadata_tools.py
│   │   ├── file_tools.py
│   │   └── tool_registry.py
│   │
│   ├── guardrails/
│   │   ├── __init__.py
│   │   ├── callbacks.py
│   │   ├── input_policy.py
│   │   ├── file_policy.py
│   │   ├── secret_policy.py
│   │   ├── prompt_injection_policy.py
│   │   └── output_policy.py
│   │
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── repository.py
│   │   ├── findings.py
│   │   ├── agent_results.py
│   │   ├── reports.py
│   │   ├── tools.py
│   │   └── settings.py
│   │
│   ├── services/
│   │   ├── __init__.py
│   │   ├── intake_service.py
│   │   ├── analysis_service.py
│   │   ├── session_service.py
│   │   ├── report_service.py
│   │   ├── error_service.py
│   │   └── logging_service.py
│   │
│   ├── config/
│   │   ├── __init__.py
│   │   └── settings.py
│   │
│   └── utils/
│       ├── __init__.py
│       ├── constants.py
│       ├── file_utils.py
│       ├── text_utils.py
│       └── time_utils.py
│
├── ui/
│   ├── streamlit_app.py
│   ├── components/
│   │   ├── __init__.py
│   │   ├── repository_input.py
│   │   ├── progress_panel.py
│   │   ├── health_score_card.py
│   │   ├── findings_panel.py
│   │   ├── recommendations_panel.py
│   │   ├── draft_preview.py
│   │   └── limitations_panel.py
│   │
│   └── pages/
│       ├── 1_Repository_Analysis.py
│       ├── 2_Recommendations.py
│       └── 3_About.py
│
├── evals/
│   ├── fixtures/
│   │   ├── weak_student_project/
│   │   ├── streamlit_ml_project/
│   │   ├── python_api_project/
│   │   ├── open_source_library/
│   │   ├── security_risk_project/
│   │   ├── prompt_injection_project/
│   │   ├── large_repository_project/
│   │   └── high_quality_project/
│   │
│   ├── expected_outputs/
│   │   ├── expected_findings.json
│   │   ├── forbidden_findings.json
│   │   └── expected_limitations.json
│   │
│   ├── datasets/
│   │   └── evaluation_cases.json
│   │
│   ├── runners/
│   │   ├── run_agent_eval.py
│   │   ├── run_safety_eval.py
│   │   └── generate_eval_report.py
│   │
│   └── reports/
│       └── .gitkeep
│
├── tests/
│   ├── unit/
│   │   ├── test_repository_intake.py
│   │   ├── test_technology_detection.py
│   │   ├── test_file_prioritization.py
│   │   ├── test_redaction.py
│   │   ├── test_scoring.py
│   │   ├── test_prioritization.py
│   │   └── test_schema_validation.py
│   │
│   ├── integration/
│   │   ├── test_github_mcp_adapter.py
│   │   ├── test_analysis_service.py
│   │   ├── test_agent_workflow.py
│   │   └── test_report_generation.py
│   │
│   ├── safety/
│   │   ├── test_prompt_injection.py
│   │   ├── test_secret_redaction.py
│   │   ├── test_tool_restrictions.py
│   │   └── test_output_sanitization.py
│   │
│   └── ui/
│       └── test_streamlit_app.py
│
├── docs/
│   ├── architecture/
│   │   ├── system_architecture.md
│   │   ├── multi_agent_architecture.md
│   │   ├── adk_design.md
│   │   └── mcp_integration.md
│   │
│   ├── product/
│   │   ├── problem_definition.md
│   │   ├── project_scope.md
│   │   ├── requirements.md
│   │   ├── user_personas.md
│   │   └── use_cases.md
│   │
│   ├── engineering/
│   │   ├── agent_skills.md
│   │   ├── security_design.md
│   │   ├── evaluation_strategy.md
│   │   ├── technology_stack.md
│   │   └── folder_structure.md
│   │
│   ├── adr/
│   │   ├── ADR-001-modular-monolith.md
│   │   ├── ADR-002-read-only-github-access.md
│   │   ├── ADR-003-streamlit-ui.md
│   │   └── ADR-004-session-only-storage.md
│   │
│   └── demo/
│       ├── demo_script.md
│       ├── demo_repositories.md
│       └── screenshots/
│
├── scripts/
│   ├── run_local.py
│   ├── run_evaluations.py
│   ├── validate_environment.py
│   └── seed_demo_data.py
│
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   └── security-tests.yml
│   │
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.yml
│   │   └── feature_request.yml
│   │
│   ├── pull_request_template.md
│   └── dependabot.yml
│
├── .env.example
├── .gitignore
├── .python-version
├── CONTRIBUTING.md
├── SECURITY.md
├── CODE_OF_CONDUCT.md
├── LICENSE
├── README.md
├── pyproject.toml
├── uv.lock
└── Dockerfile
```

---

# 14.3 Root-Level Files

| File                 | Purpose                                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------------------------------- |
| `README.md`          | Main project documentation, setup instructions, architecture overview, screenshots, demo, and contribution guidance |
| `pyproject.toml`     | Python project configuration, dependencies, test settings, linting, formatting, and type-checking configuration     |
| `uv.lock`            | Locked dependency versions for reproducible environments                                                            |
| `.env.example`       | Safe template for required environment variables                                                                    |
| `.gitignore`         | Prevents secrets, virtual environments, logs, build artifacts, and temporary files from being committed             |
| `Dockerfile`         | Containerized deployment configuration                                                                              |
| `LICENSE`            | Defines repository usage permissions                                                                                |
| `CONTRIBUTING.md`    | Contributor onboarding and contribution process                                                                     |
| `SECURITY.md`        | Security vulnerability reporting policy                                                                             |
| `CODE_OF_CONDUCT.md` | Community behavior guidelines                                                                                       |
| `.python-version`    | Defines the expected Python runtime version                                                                         |

---

# 14.4 Application Directory

The `app/` directory contains all backend and agent-system logic.

It must not contain Streamlit rendering code, test fixtures, or project documentation.

```text
app/
├── agents/
├── skills/
├── tools/
├── guardrails/
├── schemas/
├── services/
├── config/
└── utils/
```

This separation makes the application easier to test independently from the UI.

---

# 14.5 Agent Directory

```text
app/agents/
```

This directory contains one module per ADK agent.

| File                      | Responsibility                                                                                       |
| ------------------------- | ---------------------------------------------------------------------------------------------------- |
| `coordinator.py`          | Root ADK agent that delegates, synthesizes, deduplicates, and creates the final report               |
| `repository_structure.py` | Maps repository structure, identifies key files, and selects evidence                                |
| `documentation.py`        | Evaluates README and documentation completeness                                                      |
| `quality.py`              | Assesses tests, CI, linting, dependency management, and maintainability signals                      |
| `security.py`             | Performs redacted security-hygiene analysis                                                          |
| `discoverability.py`      | Reviews GitHub metadata, licensing, topics, releases, and community health files                     |
| `portfolio.py`            | Assesses demos, screenshots, project value, metrics, architecture explanation, and portfolio quality |

## Design Rule

Agents must not contain low-level GitHub API logic, raw secret-scanning logic, scoring formulas, or UI code. Those responsibilities belong in tools, skills, guardrails, and services.

---

# 14.6 Skills Directory

```text
app/skills/
```

Skills contain reusable logic that may be called by multiple agents.

| File                              | Responsibility                                                   |
| --------------------------------- | ---------------------------------------------------------------- |
| `repository_intake.py`            | Parses and validates repository input                            |
| `repository_classification.py`    | Determines broad repository category                             |
| `technology_detection.py`         | Detects languages, frameworks, package managers, and tools       |
| `file_prioritization.py`          | Selects safe high-value files for inspection                     |
| `documentation_coverage.py`       | Detects documentation section coverage                           |
| `engineering_hygiene.py`          | Detects maintainability and engineering-practice signals         |
| `security_hygiene.py`             | Detects risky paths and security-hygiene indicators              |
| `discoverability.py`              | Evaluates metadata and community-readiness signals               |
| `portfolio_readiness.py`          | Evaluates demos, metrics, architecture, and presentation signals |
| `recommendation_normalization.py` | Converts findings into a shared schema                           |
| `prioritization.py`               | Assigns priority and effort levels                               |
| `scoring.py`                      | Calculates category and overall health scores                    |
| `redaction.py`                    | Removes secret-like values from content and outputs              |
| `draft_generation.py`             | Produces safe, reviewable draft files or sections                |
| `limitations.py`                  | Creates consistent limitations and incomplete-analysis messages  |

## Design Rule

Skills should be deterministic whenever possible. LLM reasoning should remain inside agent modules and controlled draft generation.

---

# 14.7 Tools Directory

```text
app/tools/
```

The tools directory contains the controlled interface between agents and external repository data.

| File                    | Responsibility                                                       |
| ----------------------- | -------------------------------------------------------------------- |
| `github_mcp_adapter.py` | Read-only adapter for GitHub MCP server or GitHub REST API           |
| `repository_tools.py`   | Repository validation, metadata retrieval, and tree inspection tools |
| `metadata_tools.py`     | Accessors for topics, license, releases, and community metadata      |
| `file_tools.py`         | Safe selected-file retrieval with file and content limits            |
| `tool_registry.py`      | Registers only approved tools for each agent role                    |

## Security Rule

No write-capable GitHub operations may be implemented in this directory for the MVP.

---

# 14.8 Guardrails Directory

```text
app/guardrails/
```

The guardrails directory enforces security policies independently from agent prompts.

| File                         | Responsibility                                                             |
| ---------------------------- | -------------------------------------------------------------------------- |
| `callbacks.py`               | ADK lifecycle callbacks for validation, redaction, and safe error handling |
| `input_policy.py`            | Repository URL and identifier validation rules                             |
| `file_policy.py`             | File allowlists, deny lists, size limits, and path restrictions            |
| `secret_policy.py`           | Sensitive pattern rules and redaction enforcement                          |
| `prompt_injection_policy.py` | Repository-content isolation and prompt injection controls                 |
| `output_policy.py`           | Final report sanitization, schema enforcement, and safe output rules       |

## Design Rule

Security policy must not depend only on LLM instructions. Guardrails must be enforceable through deterministic code.

---

# 14.9 Schemas Directory

```text
app/schemas/
```

This directory contains Pydantic models used across the system.

| File               | Responsibility                                                    |
| ------------------ | ----------------------------------------------------------------- |
| `repository.py`    | Repository reference, metadata, tree entries, and analysis limits |
| `findings.py`      | Evidence, finding, priority, confidence, and effort models        |
| `agent_results.py` | Specialist-agent output and status models                         |
| `reports.py`       | Health score, report, recommendation, and draft-content models    |
| `tools.py`         | Tool request and response schemas                                 |
| `settings.py`      | Typed configuration models                                        |

## Design Rule

No agent output should reach the UI unless it validates against a schema from this directory.

---

# 14.10 Services Directory

```text
app/services/
```

Services coordinate deterministic application workflows.

| File                  | Responsibility                                                          |
| --------------------- | ----------------------------------------------------------------------- |
| `intake_service.py`   | Validates user input and starts repository analysis                     |
| `analysis_service.py` | Runs the Coordinator Agent and manages the end-to-end analysis workflow |
| `session_service.py`  | Manages session-scoped analysis state                                   |
| `report_service.py`   | Formats validated results into UI-ready report sections                 |
| `error_service.py`    | Converts internal errors into safe user-facing messages                 |
| `logging_service.py`  | Configures structured and redacted logging                              |

## Design Rule

Services should orchestrate application operations but should not duplicate agent reasoning or UI rendering logic.

---

# 14.11 User Interface Directory

```text
ui/
```

The UI directory contains Streamlit-specific code only.

| File or Folder     | Responsibility                         |
| ------------------ | -------------------------------------- |
| `streamlit_app.py` | Main Streamlit application entry point |
| `components/`      | Reusable visual components             |
| `pages/`           | Optional Streamlit multipage screens   |

## Suggested UI Components

| Component                  | Purpose                                                 |
| -------------------------- | ------------------------------------------------------- |
| `repository_input.py`      | Repository URL input and validation state               |
| `progress_panel.py`        | Agent status, tool progress, and analysis-stage display |
| `health_score_card.py`     | Overall and category-level health-score display         |
| `findings_panel.py`        | Finding cards grouped by category                       |
| `recommendations_panel.py` | Priority-sorted recommendation display                  |
| `draft_preview.py`         | Reviewable generated file content                       |
| `limitations_panel.py`     | Analysis scope, tool failures, and safety disclaimers   |

## Design Rule

The Streamlit UI should call application services, not individual agents or MCP tools directly.

---

# 14.12 Evaluation Directory

```text
evals/
```

The evaluation directory contains controlled fixtures, expected outputs, evaluation runners, and generated reports.

| Folder              | Purpose                                             |
| ------------------- | --------------------------------------------------- |
| `fixtures/`         | Mock repository content used for repeatable testing |
| `expected_outputs/` | Expected, forbidden, and limitation findings        |
| `datasets/`         | Structured evaluation-case definitions              |
| `runners/`          | Scripts that execute agent and safety evaluations   |
| `reports/`          | Generated evaluation summaries and metrics          |

## Fixture Examples

```text
evals/fixtures/
├── weak_student_project/
├── streamlit_ml_project/
├── python_api_project/
├── open_source_library/
├── security_risk_project/
├── prompt_injection_project/
├── large_repository_project/
└── high_quality_project/
```

This structure makes it possible to evaluate the system without depending on changing live repositories.

---

# 14.13 Tests Directory

```text
tests/
```

Tests are grouped by test type rather than by source folder.

| Folder         | Purpose                                                                       |
| -------------- | ----------------------------------------------------------------------------- |
| `unit/`        | Tests isolated skills, schemas, policies, and utilities                       |
| `integration/` | Tests MCP adapter, service interactions, and multi-agent workflow             |
| `safety/`      | Tests redaction, prompt injection, tool restrictions, and output sanitization |
| `ui/`          | Tests Streamlit form behavior and display logic where practical               |

## Minimum Safety Tests

* Token-like values are redacted.
* `.env` content is not displayed.
* Prompt injection in README files does not alter agent behavior.
* Agents cannot access write tools.
* Oversized files are excluded.
* Raw errors do not reach the UI.

---

# 14.14 Documentation Directory

```text
docs/
```

The documentation directory stores design and engineering documentation separate from the root `README.md`.

| Folder          | Purpose                                                                      |
| --------------- | ---------------------------------------------------------------------------- |
| `architecture/` | System, multi-agent, ADK, and MCP documentation                              |
| `product/`      | Problem definition, scope, requirements, personas, and use cases             |
| `engineering/`  | Skills, security, evaluation, technology, and folder structure documentation |
| `adr/`          | Architecture Decision Records                                                |
| `demo/`         | Demo script, sample repositories, screenshots, and presentation notes        |

## Architecture Decision Records

ADRs document important choices and their reasoning.

Recommended initial ADRs:

| ADR     | Decision                                                      |
| ------- | ------------------------------------------------------------- |
| ADR-001 | Use modular monolith architecture                             |
| ADR-002 | Restrict GitHub integration to read-only public repositories  |
| ADR-003 | Use Streamlit for the MVP interface                           |
| ADR-004 | Use session-only storage for repository analysis              |
| ADR-005 | Use deterministic guardrails and scoring alongside LLM agents |

---

# 14.15 Scripts Directory

```text
scripts/
```

Scripts provide repeatable developer workflows.

| File                      | Purpose                                                |
| ------------------------- | ------------------------------------------------------ |
| `run_local.py`            | Starts the application locally                         |
| `run_evaluations.py`      | Runs evaluation fixtures and exports results           |
| `validate_environment.py` | Checks required environment variables and dependencies |
| `seed_demo_data.py`       | Prepares controlled demo fixtures or cached outputs    |

Scripts should not contain core business logic. They should call reusable modules from `app/`.

---

# 14.16 GitHub Automation Directory

```text
.github/
```

This directory supports project quality and collaboration.

| File or Folder                 | Purpose                                                  |
| ------------------------------ | -------------------------------------------------------- |
| `workflows/ci.yml`             | Runs linting, type checks, tests, and schema validation  |
| `workflows/security-tests.yml` | Runs redaction and prompt-injection safety tests         |
| `ISSUE_TEMPLATE/`              | Standardizes bug reports and feature requests            |
| `pull_request_template.md`     | Guides contributors through safe, testable pull requests |
| `dependabot.yml`               | Automates dependency update proposals                    |

---

# 14.17 Data Flow Through the Folder Structure

```text
Streamlit UI
      ↓
Application Services
      ↓
Coordinator Agent
      ↓
Specialist Agents
      ↓
Skills + Guardrails
      ↓
GitHub MCP Tools
      ↓
Structured Schemas
      ↓
Report Service
      ↓
Streamlit UI
```

This flow ensures that:

* The UI remains separate from agent logic.
* Agents remain separate from external tool implementation.
* Guardrails protect all repository content before it reaches agents.
* Schemas validate outputs before reports are rendered.
* Tests and evaluations can run independently of the interface.

---

# 14.18 Folder Structure Design Decisions

| Decision                      | Chosen Approach       | Reason                                                           |
| ----------------------------- | --------------------- | ---------------------------------------------------------------- |
| Architecture style            | Modular monolith      | Fast MVP delivery with clean internal boundaries                 |
| Agent code location           | `app/agents/`         | Keeps ADK agent definitions isolated and discoverable            |
| Shared logic location         | `app/skills/`         | Encourages reuse and independent testing                         |
| External integration location | `app/tools/`          | Creates a controlled MCP and GitHub access boundary              |
| Security code location        | `app/guardrails/`     | Makes safety controls explicit and auditable                     |
| Typed contracts location      | `app/schemas/`        | Ensures structured data across the system                        |
| UI isolation                  | `ui/`                 | Prevents Streamlit code from leaking into core application logic |
| Test separation               | `tests/` and `evals/` | Distinguishes software tests from agent-quality evaluation       |
| Documentation organization    | `docs/`               | Supports professional open-source repository standards           |

---
