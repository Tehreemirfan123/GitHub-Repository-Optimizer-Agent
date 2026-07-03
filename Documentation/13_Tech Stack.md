# Technology Stack

### 13.1 Purpose

This document defines the technology stack for the GitHub Repository Optimizer Agent capstone MVP.

The selected stack prioritizes:

* Native alignment with Google ADK and Gemini.
* Fast implementation within the capstone timeline.
* Safe, read-only GitHub repository analysis.
* Clear multi-agent orchestration.
* Structured outputs and evaluation.
* A polished, easy-to-demo user interface.
* Maintainability beyond the competition.

The stack intentionally avoids unnecessary infrastructure such as microservices, distributed queues, databases, Kubernetes, and autonomous GitHub write access.

---

# 13.2 Technology Stack Overview

| Layer                 | Selected Technology                                        | Purpose                                                                                |
| --------------------- | ---------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Primary Language      | Python                                                     | Core application, agent logic, tools, testing, and UI integration                      |
| Agent Framework       | Google Agent Development Kit (ADK)                         | Multi-agent orchestration, tools, sessions, callbacks, and evaluation                  |
| Foundation Model      | Gemini through Google ADK                                  | Agent reasoning, synthesis, recommendation generation, and draft content generation    |
| Gemini SDK            | Google GenAI SDK                                           | Official Gemini API client where direct SDK use is needed                              |
| User Interface        | Streamlit                                                  | Interactive repository input, progress display, health dashboard, findings, and drafts |
| MCP Protocol          | Model Context Protocol                                     | Standardized external-tool integration boundary                                        |
| GitHub Integration    | Read-only GitHub MCP server or GitHub API adapter          | Public metadata, file tree, selected file content, topics, releases, and workflows     |
| Data Validation       | Pydantic                                                   | Structured models for findings, agent outputs, reports, and tool responses             |
| HTTP Client           | `httpx`                                                    | Controlled GitHub API and MCP-related HTTP requests where required                     |
| Configuration         | `pydantic-settings` and environment variables              | Typed configuration and secure secret handling                                         |
| Testing               | `pytest`                                                   | Unit, integration, safety, and evaluation tests                                        |
| Mocking               | `pytest-mock` or `unittest.mock`                           | Simulated GitHub, MCP, model, and failure responses                                    |
| Code Quality          | Ruff and Pyright                                           | Linting, formatting support, and static type checking                                  |
| Logging               | Python `logging` with structured JSON-style records        | Safe observability and debugging                                                       |
| Dependency Management | `uv` or `pip` with lock file                               | Reproducible local setup                                                               |
| Deployment            | Streamlit Community Cloud, Google Cloud Run, or local demo | Capstone demonstration and lightweight deployment                                      |
| CI/CD                 | GitHub Actions                                             | Automated tests, linting, and type checks                                              |

---

# 13.3 Primary Language — Python

Python is the primary implementation language for the MVP.

## Why Python Is Selected

Python is the most practical choice because the system requires:

* Google ADK integration.
* Gemini model access.
* Streamlit interface development.
* MCP client or server integration.
* Data validation.
* Test automation.
* Repository-analysis utilities.
* Security redaction logic.
* Rapid prototyping with maintainable code.

The official ADK documentation provides Python development guidance, while the MCP Python SDK supports building MCP clients and servers using standard transports.

## Alternatives Considered

| Alternative        | Decision     | Reason                                                                                    |
| ------------------ | ------------ | ----------------------------------------------------------------------------------------- |
| TypeScript         | Deferred     | Strong for web applications, but would split the stack between frontend and agent backend |
| Java               | Not selected | More implementation overhead for the capstone timeline                                    |
| Go                 | Not selected | Suitable for services, but less aligned with Streamlit and rapid agent prototyping        |
| Multiple languages | Avoided      | Increases complexity, testing effort, and context switching                               |

## Recommended Runtime

Use a currently supported Python version compatible with the selected ADK, Streamlit, MCP SDK, and deployment environment. The MCP Python server guidance requires Python 3.10 or higher.

---

# 13.4 Agent Framework — Google ADK

Google Agent Development Kit (ADK) is the core multi-agent framework.

## Responsibilities

ADK will be used for:

* Root Coordinator Agent definition.
* Specialist sub-agent definitions.
* Agent delegation.
* Tool integration.
* Session and state management.
* Callback-based guardrails.
* Structured agent outputs.
* Agent evaluation workflows.

ADK supports model integration, tools, agent composition, sessions, callbacks, and evaluation-oriented workflows, which directly supports the architecture defined in earlier stages.

## Why ADK Is Selected

* It is directly aligned with the competition’s multi-agent requirement.
* It supports a root-agent and specialist-agent design.
* It keeps agents, tools, sessions, and callbacks in one framework.
* It supports Gemini models naturally.
* It provides a credible production-oriented architecture for the GitHub portfolio.

## Alternatives Considered

| Alternative          | Decision | Reason                                                                                         |
| -------------------- | -------- | ---------------------------------------------------------------------------------------------- |
| LangGraph            | Deferred | Powerful for workflow graphs but less directly aligned with the course requirement             |
| CrewAI               | Deferred | Useful for role-based collaboration, but ADK better demonstrates Google ecosystem capabilities |
| AutoGen              | Deferred | Suitable for multi-agent experimentation but adds unnecessary framework divergence             |
| Single Gemini prompt | Rejected | Does not adequately demonstrate agent specialization, orchestration, or evaluation             |

---

# 13.5 Model Strategy — Gemini

Gemini models will provide contextual reasoning, repository assessment, report synthesis, and draft content generation.

Google’s official guidance recommends the Google GenAI SDK as the production-ready SDK for Gemini API development.

## Model Allocation Strategy

| Workload                         | Preferred Approach                                                  |
| -------------------------------- | ------------------------------------------------------------------- |
| Input validation                 | Deterministic Python; no model                                      |
| Repository file classification   | Deterministic rules first; lightweight model only when ambiguous    |
| Technology detection             | Deterministic manifest and file-extension inspection                |
| Documentation assessment         | Fast Gemini model                                                   |
| Quality and portfolio assessment | Fast Gemini model                                                   |
| Security interpretation          | Deterministic redaction plus Gemini interpretation of safe evidence |
| Final recommendation synthesis   | More capable Gemini model                                           |
| Draft documentation generation   | More capable Gemini model                                           |
| Health-score calculation         | Deterministic Python                                                |

## Model Design Principle

Use models only where contextual reasoning is required.

Tasks such as URL parsing, file filtering, scoring, secret redaction, size-limit enforcement, and schema validation must remain deterministic. This reduces hallucination risk, cost, and evaluation difficulty.

## Model Configuration Policy

Model names must be stored in environment configuration rather than hard-coded throughout the application.

```text id="tech01"
FAST_MODEL_NAME=
SYNTHESIS_MODEL_NAME=
GOOGLE_API_KEY=
```

The selected Gemini model identifiers should be finalized during implementation based on current ADK and Gemini availability, pricing, quota, and performance testing.

---

# 13.6 User Interface — Streamlit

Streamlit will be used for the capstone MVP interface.

## Responsibilities

The Streamlit application will provide:

* Repository URL or `owner/repository` input.
* Validation feedback.
* Analysis progress indicators.
* Agent status display.
* Repository overview.
* Health-score dashboard.
* Category-level findings.
* Prioritized recommendations.
* Draft-content preview and copy workflow.
* Limitations and safety disclaimers.

Streamlit is an open-source Python framework for interactive data applications. Its session state supports values scoped to an individual user session, which fits the MVP’s session-based analysis model.

## Why Streamlit Is Selected

* Fast to build and iterate.
* Native Python integration.
* Strong fit for structured reports, tables, metrics, progress indicators, and code blocks.
* Easy to demonstrate in a competition environment.
* Avoids maintaining a separate frontend framework and backend API during the MVP stage.

## Streamlit Design Rules

* Use `st.form` for repository submission to avoid unnecessary reruns.
* Use session state only for session-specific metadata, progress, and final reports.
* Do not place tokens, raw repository content, or secrets in session state.
* Render structured findings rather than raw agent messages.
* Display severity using labels and icons, not color alone.

Streamlit forms batch user input into a single rerun, and session state is scoped to an individual session.

## Alternatives Considered

| Alternative      | Decision      | Reason                                                        |
| ---------------- | ------------- | ------------------------------------------------------------- |
| React + FastAPI  | Future option | More flexible but significantly more implementation effort    |
| Next.js          | Deferred      | Excellent production frontend option, but unnecessary for MVP |
| CLI only         | Rejected      | Less compelling for a capstone demo and portfolio project     |
| Jupyter notebook | Rejected      | Weak user experience for interactive repository analysis      |

---

# 13.7 MCP and GitHub Integration

The Model Context Protocol will provide the standardized boundary between agents and GitHub data access.

MCP defines how servers expose tools that language models can invoke, with each tool described by a name and schema.

## Selected Approach

Use one of the following implementations behind a common adapter interface:

1. A read-only GitHub MCP server accessed through STDIO; or
2. A direct GitHub REST API adapter that exposes the same internal tool contracts.

## Why an Adapter Layer Is Required

The internal adapter ensures that agent code does not depend directly on a particular MCP server implementation.

It will provide:

* Input normalization.
* Read-only tool allowlisting.
* File-path validation.
* File-type filtering.
* File-size and total-content limits.
* Rate-limit handling.
* Timeout handling.
* Secret redaction hooks.
* Safe error translation.
* Stable structured responses.

## MCP SDK

The official MCP Python SDK supports Python clients and servers, standard transports, and type-safe protocol interactions.

## GitHub Access Policy

The MVP supports public repositories only.

Allowed operations:

* Retrieve repository metadata.
* Retrieve repository file tree.
* Retrieve selected text file content.
* Retrieve README, topics, license, releases, workflows, and community metadata.

Forbidden operations:

* Push commits.
* Create branches.
* Create pull requests.
* Modify settings.
* Delete files.
* Access private repositories.
* Execute repository code.

---

# 13.8 Data Models and Validation — Pydantic

Pydantic will be used for typed data contracts across the application.

## Responsibilities

* Validate repository input.
* Validate GitHub/MCP responses.
* Define structured agent findings.
* Define agent result schemas.
* Define health-score schemas.
* Validate final report output.
* Validate configuration values.

## Core Models

| Model                 | Purpose                                            |
| --------------------- | -------------------------------------------------- |
| `RepositoryRef`       | Normalized repository owner, name, URL, and branch |
| `RepositoryMetadata`  | Public GitHub metadata                             |
| `RepositoryTreeEntry` | File and directory metadata                        |
| `Evidence`            | File, metadata, or missing-artifact evidence       |
| `Finding`             | One normalized agent finding                       |
| `AgentResult`         | Structured specialist-agent result                 |
| `HealthScore`         | Category and overall heuristic scores              |
| `OptimizationReport`  | Final UI-ready report                              |
| `AnalysisLimits`      | File, time, and retrieval constraints              |

## Why Pydantic Is Selected

Structured outputs are essential for:

* Agent-result validation.
* Reliable synthesis.
* Deduplication.
* Deterministic scoring.
* Safer UI rendering.
* Automated evaluation.
* Preventing malformed or unsupported agent output from reaching users.

---

# 13.9 Configuration and Secret Management

## Selected Components

* `pydantic-settings`
* Environment variables
* `.env.example`
* Deployment-platform secrets
* Local `.env` file excluded through `.gitignore`

## Required Environment Variables

```text id="tech02"
GOOGLE_API_KEY=
GITHUB_TOKEN=
FAST_MODEL_NAME=
SYNTHESIS_MODEL_NAME=
MCP_TRANSPORT=stdio

MAX_REPOSITORY_FILES=500
MAX_SELECTED_FILES=25
MAX_FILE_SIZE_BYTES=100000
MAX_TOTAL_CONTENT_BYTES=1000000
TOOL_TIMEOUT_SECONDS=15
MAX_ANALYSIS_SECONDS=120
```

## Security Rules

* Never commit `.env`.
* Never include secret values in logs, reports, tests, or screenshots.
* Use a read-only GitHub token only when required.
* Never expose GitHub tokens to prompts, agent session state, or UI components.
* Validate configuration at application startup.
* Fail safely when required configuration is missing.

---

# 13.10 Testing and Quality Tooling

## Testing Stack

| Tool                             | Purpose                                         |
| -------------------------------- | ----------------------------------------------- |
| `pytest`                         | Unit, integration, safety, and evaluation tests |
| `pytest-cov`                     | Test coverage reporting                         |
| `pytest-mock` or `unittest.mock` | Mock MCP, GitHub, and model calls               |
| Fixture repositories             | Repeatable repository-analysis evaluation       |
| Streamlit `AppTest`              | UI behavior testing where practical             |

Streamlit provides testing support through `AppTest`, including control over session state and secrets during tests.

## Code Quality Stack

| Tool           | Purpose                                   |
| -------------- | ----------------------------------------- |
| Ruff           | Fast linting and formatting workflow      |
| Pyright        | Static type checking                      |
| Pre-commit     | Local quality checks before commits       |
| GitHub Actions | Automated checks on push and pull request |

## Minimum CI Pipeline

```text id="tech03"
1. Install dependencies
2. Run Ruff checks
3. Run Pyright checks
4. Run pytest unit tests
5. Run safety and redaction tests
6. Run schema-validation tests
```

---

# 13.11 Logging and Observability

## Selected Technology

Use Python’s standard `logging` module with structured JSON-compatible messages.

## Logged Events

* Analysis session start and completion.
* Repository identifier.
* Agent execution status.
* Tool-call name and status.
* Tool latency.
* Number of selected files.
* Number of redactions.
* Number of findings.
* Partial-analysis limitations.
* Safe error category.

## Do Not Log

* Tokens.
* Passwords.
* Private keys.
* Raw secret-like values.
* Full repository file contents.
* Raw internal reasoning.
* Raw stack traces in user-facing output.

## Future Option

OpenTelemetry-compatible tracing can be introduced after the capstone for distributed observability, but is intentionally excluded from the MVP to avoid infrastructure overhead.

---

# 13.12 Dependency Management

## Recommended Approach

Use `uv` for fast environment and dependency management, with a committed lock file for reproducibility.

If the deployment environment or team workflow requires traditional tooling, use `pip` with a pinned `requirements.txt` or `pyproject.toml` lock-based workflow.

## Required Dependency Categories

```text id="tech04"
google-adk
google-genai
mcp
streamlit
pydantic
pydantic-settings
httpx
pytest
pytest-cov
ruff
pyright
python-dotenv
```

Exact package versions should be pinned after the first successful end-to-end implementation, then updated only through tested dependency changes.

---

# 13.13 Deployment Strategy

## MVP Deployment Options

| Option                    | Recommended Use                                                       |
| ------------------------- | --------------------------------------------------------------------- |
| Local Streamlit           | Development and live demonstration                                    |
| Streamlit Community Cloud | Fast public demo where secrets and process constraints are compatible |
| Google Cloud Run          | Preferred portfolio-ready deployment option                           |
| Docker container          | Reproducible local and Cloud Run deployment                           |

## Recommended Capstone Sequence

1. Develop locally with Streamlit.
2. Run agent and MCP integration locally through STDIO.
3. Package the application with Docker.
4. Deploy to Cloud Run if deployment time and credentials permit.
5. Maintain a local demo fallback for reliability.

## Why Cloud Run Is a Strong Post-MVP Choice

Cloud Run provides a more production-oriented deployment story without requiring Kubernetes management. However, a local Streamlit deployment remains the safest capstone fallback because it minimizes deployment and credential risk.

---

# 13.14 Architecture-to-Technology Mapping

| Architecture Component          | Technology                                                 |
| ------------------------------- | ---------------------------------------------------------- |
| Presentation Layer              | Streamlit                                                  |
| Application Service Layer       | Python modules and service classes                         |
| Multi-Agent Orchestration Layer | Google ADK                                                 |
| Root Coordinator Agent          | ADK `LlmAgent` or equivalent                               |
| Specialist Agents               | Individual ADK sub-agent definitions                       |
| Tool Gateway                    | Python adapter and policy-enforcement services             |
| GitHub Integration              | MCP server or GitHub REST API adapter                      |
| Session State                   | ADK session state plus Streamlit session state for UI only |
| Structured Outputs              | Pydantic                                                   |
| Guardrails                      | ADK callbacks plus deterministic Python policies           |
| Scoring Engine                  | Deterministic Python skill                                 |
| Draft Content Generation        | Gemini through ADK                                         |
| Tests and Evaluation            | Pytest, fixtures, structured expected outputs              |
| CI/CD                           | GitHub Actions                                             |
| Deployment                      | Streamlit local or Cloud Run                               |

---

# 13.15 Key Technology Decisions

| Decision Area      | Selected Option                 | Rationale                                                              |
| ------------------ | ------------------------------- | ---------------------------------------------------------------------- |
| Main language      | Python                          | One language across agents, tools, evaluation, and UI                  |
| Agent framework    | Google ADK                      | Directly supports capstone requirements and Google ecosystem alignment |
| Model provider     | Gemini                          | Native ADK alignment and strong reasoning for synthesis                |
| Interface          | Streamlit                       | Rapid, polished, Python-native capstone experience                     |
| External tools     | MCP with GitHub adapter         | Controlled, interoperable, read-only tool integration                  |
| Data validation    | Pydantic                        | Reliable structured outputs and safer report generation                |
| Repository storage | Session-only MVP                | Reduces privacy and infrastructure complexity                          |
| Security policy    | Read-only public analysis       | Minimizes permission and modification risk                             |
| Testing            | Pytest + fixtures               | Repeatable, evidence-based evaluation                                  |
| Deployment         | Local first, Cloud Run optional | Reliable demo with a production-ready upgrade path                     |

---

# 13.16 Explicitly Deferred Technologies

The following are intentionally excluded from the MVP:

* PostgreSQL or other persistent databases.
* Redis queues or background workers.
* Kubernetes.
* Microservice architecture.
* Authentication system for users.
* GitHub OAuth for private repositories.
* GitHub App write permissions.
* Vector databases.
* Embedding-based repository search.
* Autonomous code execution sandboxes.
* Complex analytics platforms.
* Distributed tracing infrastructure.
* Separate React frontend.

These technologies may be appropriate in a production roadmap but would slow implementation without proportionate capstone value.

---
