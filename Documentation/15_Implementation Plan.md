# Implementation Plan

### 15.1 Purpose

This document defines the implementation plan for the GitHub Repository Optimizer Agent.

The plan converts the approved product, architecture, multi-agent, MCP, security, evaluation, technology, and folder-structure decisions into a realistic build sequence.

The implementation strategy follows a **thin-slice-first approach**:

1. Build one complete, safe end-to-end repository analysis flow.
2. Add specialist agents and reusable skills incrementally.
3. Add security controls before expanding repository access.
4. Validate the system with fixtures and tests.
5. Polish the interface, documentation, demo, and submission materials.

The goal is not to build every possible feature. The goal is to deliver a reliable, demonstrable, production-oriented MVP.

---

# 15.2 Implementation Principles

The implementation must follow these principles:

1. **Build vertical slices, not isolated components**
   Every milestone should produce a usable improvement to the product.

2. **Start with deterministic logic**
   Validation, file selection, scoring, redaction, and schemas should be implemented before relying on LLM reasoning.

3. **Keep GitHub access read-only**
   Do not implement repository write operations in the MVP.

4. **Add agents gradually**
   Validate each specialist agent independently before connecting all agents to the Coordinator.

5. **Treat security as a release requirement**
   Secret redaction, prompt-injection handling, file restrictions, and safe errors must be implemented early.

6. **Use fixtures before live repositories**
   Controlled evaluation fixtures should be the primary testing environment.

7. **Keep the UI thin**
   Streamlit should display validated reports, not contain business logic or direct MCP calls.

8. **Document decisions as implementation progresses**
   Documentation must reflect the actual implementation, not only intended architecture.

---

# 15.3 MVP Delivery Sequence

```text id="impl01"
Project Foundation
        ↓
Safe Repository Intake
        ↓
GitHub MCP Adapter and File Controls
        ↓
Single-Agent Thin Slice
        ↓
Multi-Agent Specialist Analysis
        ↓
Recommendation Scoring and Synthesis
        ↓
Security Guardrails and Safety Tests
        ↓
Streamlit Dashboard
        ↓
Evaluation Fixtures and Metrics
        ↓
Documentation, Demo, and Submission
```

---

# 15.4 Milestone 1 — Project Foundation

## Objective

Create a clean, reproducible development environment and repository structure before implementing agent behavior.

## Tasks

* Initialize the GitHub repository.
* Create the approved folder structure.
* Configure Python project tooling.
* Add dependency management.
* Add environment-variable configuration.
* Create `.env.example`.
* Create `.gitignore`.
* Add baseline README.
* Configure Ruff, Pyright, and Pytest.
* Add pre-commit hooks if time permits.
* Add initial GitHub Actions CI workflow.

## Deliverables

* Working local Python environment.
* `pyproject.toml` or equivalent dependency configuration.
* `uv.lock` or pinned dependency file.
* CI workflow that runs linting and tests.
* Basic project README with setup instructions.
* Empty module structure for agents, tools, skills, guardrails, schemas, services, and UI.

## Acceptance Criteria

* The application imports successfully.
* Tests run successfully, even if only placeholder tests exist.
* Linting and type checks run locally.
* `.env` files are excluded from Git.
* CI runs automatically on push.

---

# 15.5 Milestone 2 — Core Schemas and Configuration

## Objective

Define the typed contracts used throughout the application.

## Tasks

* Create Pydantic models for repository references.
* Create repository metadata and file-tree schemas.
* Create finding, evidence, priority, confidence, and effort schemas.
* Create specialist-agent result schemas.
* Create health-score and final-report schemas.
* Create typed application settings.
* Define analysis limits.
* Add schema validation tests.

## Core Models

| Model                 | Purpose                                      |
| --------------------- | -------------------------------------------- |
| `RepositoryRef`       | Normalized GitHub repository identifier      |
| `RepositoryMetadata`  | Public repository metadata                   |
| `RepositoryTreeEntry` | File and directory metadata                  |
| `Evidence`            | File, metadata, or missing-artifact evidence |
| `Finding`             | One normalized repository finding            |
| `AgentResult`         | Result returned by one specialist agent      |
| `HealthScore`         | Category-level and overall heuristic score   |
| `OptimizationReport`  | Final user-facing report                     |
| `AnalysisLimits`      | File, content, time, and tool-call limits    |
| `Settings`            | Runtime configuration and model settings     |

## Acceptance Criteria

* Invalid finding objects fail validation.
* Priority, confidence, and effort fields use controlled values.
* Final reports cannot be created without required sections.
* Settings fail safely when required configuration is missing.

---

# 15.6 Milestone 3 — Repository Intake and Validation

## Objective

Allow users to submit a public GitHub repository safely.

## Tasks

* Implement repository URL parsing.
* Support `owner/repository` input.
* Reject malformed input.
* Reject unsupported Git hosting domains.
* Normalize valid repository references.
* Add public-repository validation through the GitHub adapter.
* Create user-safe error messages.
* Add unit tests for valid and invalid inputs.

## Example Supported Inputs

```text id="impl02"
https://github.com/owner/repository
https://github.com/owner/repository/
owner/repository
```

## Example Unsupported Inputs

```text id="impl03"
git@github.com:owner/repository.git
https://gitlab.com/owner/repository
owner
owner/repository/extra
```

## Acceptance Criteria

* Valid GitHub input produces a normalized `RepositoryRef`.
* Invalid input returns a plain-language error.
* Private or inaccessible repositories are rejected safely.
* No analysis begins before validation completes.

---

# 15.7 Milestone 4 — GitHub MCP Adapter and Tool Gateway

## Objective

Implement controlled, read-only GitHub access.

## Tasks

* Build the GitHub MCP adapter interface.
* Implement repository metadata retrieval.
* Implement repository-tree retrieval.
* Implement selected file-content retrieval.
* Add file-path validation.
* Add file-type allowlist and denylist.
* Add maximum file-size limits.
* Add total-content limits.
* Add timeouts and retry controls.
* Add safe error normalization.
* Add session-level caching.
* Add tool-call logging without sensitive content.

## MVP Tool Set

| Tool                        | Required |
| --------------------------- | -------- |
| Validate repository         | Yes      |
| Get repository metadata     | Yes      |
| Get repository tree         | Yes      |
| Get README content          | Yes      |
| Get approved file content   | Yes      |
| Get topics and license      | Yes      |
| Get workflow metadata       | Yes      |
| Get release metadata        | Optional |
| Get community-file metadata | Optional |

## Acceptance Criteria

* The adapter supports only read operations.
* Unsupported write operations do not exist in the tool registry.
* Binary, large, generated, and blocked files are skipped safely.
* Tool failures return safe structured errors.
* Repeated file requests use session cache where available.

---

# 15.8 Milestone 5 — Deterministic Repository Analysis Skills

## Objective

Implement the reusable non-LLM skills that support reliable analysis.

## Tasks

* Implement repository classification.
* Implement technology detection.
* Implement file prioritization.
* Implement documentation-section coverage detection.
* Implement engineering-hygiene signal detection.
* Implement discoverability signal detection.
* Implement portfolio-readiness signal detection.
* Implement secret redaction.
* Implement recommendation normalization.
* Implement prioritization rules.
* Implement health-score calculation.
* Implement limitation reporting.

## Priority Order

| Priority | Skill                                     |
| -------- | ----------------------------------------- |
| First    | Repository intake and file prioritization |
| First    | Technology detection                      |
| First    | Secret redaction                          |
| First    | Recommendation normalization              |
| First    | Prioritization and scoring                |
| Second   | Documentation coverage                    |
| Second   | Engineering hygiene                       |
| Second   | Discoverability                           |
| Second   | Portfolio readiness                       |
| Third    | Draft generation support                  |

## Acceptance Criteria

* Each skill has unit tests.
* Skills return structured, schema-valid outputs.
* Redaction is applied before agent context is created.
* Scoring is deterministic for the same finding set.
* File selection respects configured limits.

---

# 15.9 Milestone 6 — End-to-End Thin Slice

## Objective

Deliver one complete repository analysis before implementing all specialist agents.

## Scope

The first thin slice should include:

* Repository input.
* Repository validation.
* Metadata retrieval.
* File-tree retrieval.
* README retrieval.
* Technology detection.
* Documentation coverage analysis.
* One documentation-focused agent.
* One structured recommendation.
* One health-score category.
* Basic Streamlit display.

## Why This Milestone Matters

A thin slice proves that the system can:

* Accept real input.
* Access GitHub safely.
* Analyze evidence.
* Use an agent appropriately.
* Produce structured output.
* Display useful results.

This is more valuable than creating many incomplete agents in parallel.

## Acceptance Criteria

* A user can submit a public repository.
* The system identifies at least one meaningful documentation gap.
* The report includes repository metadata, evidence, recommendation, and limitation section.
* The UI displays a validated result.
* No raw repository content or secret-like value is shown unnecessarily.

---

# 15.10 Milestone 7 — Google ADK Coordinator and Specialist Agents

## Objective

Implement the multi-agent architecture after the thin slice is stable.

## Implementation Order

1. Repository Structure Agent
2. Documentation Agent
3. Quality Agent
4. Security Agent
5. Discoverability Agent
6. Portfolio Readiness Agent
7. Coordinator Agent synthesis workflow

## Agent Implementation Tasks

For each agent:

* Define the ADK agent configuration.
* Write narrow instructions.
* Define allowed tools.
* Define required context.
* Define output schema.
* Define limitations policy.
* Add fixture-based agent tests.
* Add failure simulation tests.

## Recommended Initial Agent Scope

| Agent                 | Minimum MVP Analysis                                            |
| --------------------- | --------------------------------------------------------------- |
| Structure Agent       | File inventory, key artifacts, likely repository category       |
| Documentation Agent   | README and setup documentation gaps                             |
| Quality Agent         | Tests, CI, manifests, linting, environment-template signals     |
| Security Agent        | `.env`, `.gitignore`, secret-like patterns, `SECURITY.md`       |
| Discoverability Agent | Description, topics, license, community files                   |
| Portfolio Agent       | Problem statement, demo, architecture, metrics, reproducibility |

## Acceptance Criteria

* Each agent produces schema-valid findings.
* Each agent has restricted tool access.
* Agents identify limitations when required evidence is unavailable.
* Agents do not duplicate deterministic skill behavior unnecessarily.
* No agent can modify a repository.

---

# 15.11 Milestone 8 — Coordinator Synthesis and Final Report

## Objective

Combine specialist findings into one useful and non-repetitive report.

## Tasks

* Implement agent-result collection.
* Implement duplicate finding detection.
* Implement conflict resolution rules.
* Normalize all findings.
* Prioritize recommendations.
* Calculate category scores.
* Calculate overall health score.
* Add report-level limitations.
* Add security disclaimer.
* Validate the final Pydantic report schema.
* Add report-formatting service.

## Coordinator Rules

The Coordinator must:

* Preserve evidence from specialist agents.
* Combine overlapping findings.
* Avoid duplicate recommendations.
* Show critical and high-priority findings first.
* Preserve incomplete agent status.
* Avoid unsupported conclusions.
* State that health scores are heuristic.

## Acceptance Criteria

* The report includes all required sections.
* Findings have evidence, priority, effort, and recommended action.
* Duplicate recommendations are reduced.
* Security findings remain redacted.
* Partial failures appear in limitations.
* The same fixture produces consistent report structure.

---

# 15.12 Milestone 9 — Security Guardrails

## Objective

Implement security controls before broadening testing or deployment.

## Tasks

* Implement input-policy checks.
* Implement file-policy checks.
* Implement secret redaction.
* Implement prompt-injection policy.
* Implement tool allowlisting.
* Implement output sanitization.
* Implement safe error translation.
* Implement redacted structured logging.
* Add ADK callbacks where applicable.
* Add adversarial test fixtures.

## Required Safety Tests

| Test                              | Expected Result                                   |
| --------------------------------- | ------------------------------------------------- |
| README contains prompt injection  | Agent ignores instructions and continues analysis |
| `.env` contains token-like value  | Value is redacted                                 |
| Private key block appears in file | Key content is blocked or fully redacted          |
| File exceeds size limit           | File is skipped and limitation recorded           |
| Agent attempts write operation    | Tool call is blocked                              |
| GitHub tool returns raw failure   | User receives safe error message                  |
| Redaction fails                   | Content is blocked from agent and UI              |

## Acceptance Criteria

* No raw secret appears in findings, report, logs, or UI.
* No repository code is executed.
* No write operation can be called.
* Prompt-injection content does not alter workflow.
* Safety failures block release.

---

# 15.13 Milestone 10 — Streamlit User Interface

## Objective

Create a polished, easy-to-understand capstone interface.

## Core Screens

| Screen              | Purpose                                                 |
| ------------------- | ------------------------------------------------------- |
| Repository Analysis | Submit repository and view progress                     |
| Recommendations     | Review prioritized findings and generated drafts        |
| About               | Explain architecture, safety boundaries, and evaluation |

## UI Components

* Repository input form.
* Validation status.
* Agent execution progress.
* Repository overview card.
* Technology stack chips or labels.
* Overall health-score card.
* Category score display.
* Finding cards grouped by category.
* Recommendation table or expandable panels.
* Draft-content preview.
* Limitations and disclaimer panel.

## UX Requirements

* Use clear headings and consistent severity labels.
* Avoid displaying raw agent output.
* Show analysis state clearly.
* Explain score limitations.
* Show partial-analysis limitations visibly.
* Make generated drafts easy to copy.
* Keep the interface responsive and uncluttered.

## Acceptance Criteria

* The user can submit a repository without editing code.
* Results are readable and grouped logically.
* Critical findings are visible immediately.
* Analysis limitations are not hidden.
* Generated content is clearly marked as a draft.

---

# 15.14 Milestone 11 — Draft Content Generation

## Objective

Allow the user to request safe, repository-aware drafts for selected recommendations.

## Supported First Draft Types

1. README installation section.
2. README usage section.
3. README architecture section.
4. `.env.example`.
5. `.gitignore` additions.
6. `SECURITY.md`.
7. `CONTRIBUTING.md`.

## Implementation Rules

* Draft generation requires an explicit user action.
* Drafts use only inspected and validated repository evidence.
* Drafts must not invent commands, deployment URLs, results, or technologies.
* Drafts must never contain sensitive values.
* Drafts are displayed as reviewable content only.
* Drafts are not written to GitHub automatically.

## Acceptance Criteria

* The system generates a useful draft for at least three supported artifact types.
* Generated content follows repository context.
* Every draft includes placement guidance.
* Every draft is labeled “Review Before Applying.”

---

# 15.15 Milestone 12 — Evaluation Fixtures and Automated Evaluation

## Objective

Create evidence that the agent system works reliably and safely.

## Required Fixtures

| Fixture                         | Required Evaluation Focus                                         |
| ------------------------------- | ----------------------------------------------------------------- |
| Weak student project            | Missing README sections, tests, and setup guidance                |
| Streamlit ML project            | Reproducibility, model documentation, metrics, and portfolio gaps |
| Python API project              | API documentation, `.env.example`, tests, and CI                  |
| Open-source library             | License, topics, contribution files, templates                    |
| Security-risk fixture           | Secret redaction and `.gitignore` detection                       |
| Prompt-injection fixture        | Instruction isolation and safe behavior                           |
| Large repository fixture        | Sampling and limitation reporting                                 |
| High-quality repository fixture | Low false-positive rate                                           |

## Evaluation Tasks

* Define expected findings.
* Define forbidden findings.
* Define expected priority ranges.
* Define expected limitations.
* Run specialist-agent evaluations.
* Run Coordinator synthesis evaluations.
* Run safety evaluations.
* Record metrics.
* Generate evaluation reports.

## Minimum Evaluation Metrics

* Finding precision.
* Finding recall.
* Evidence grounding rate.
* Priority alignment.
* Duplicate finding rate.
* Agent completion rate.
* Secret-leak rate.
* Prompt-injection resistance.
* Schema-valid report rate.
* Average runtime.

## Acceptance Criteria

* All fixtures produce schema-valid reports.
* No secret leakage occurs.
* Prompt-injection tests pass.
* High-quality fixture does not receive excessive negative findings.
* Evaluation results are documented in the repository.

---

# 15.16 Milestone 13 — CI, Documentation, and Repository Polish

## Objective

Prepare the project as a professional open-source and portfolio repository.

## Tasks

* Complete README.
* Add architecture diagram.
* Add multi-agent workflow diagram.
* Add MCP integration diagram.
* Add screenshots or demo GIF.
* Add contribution guidelines.
* Add security policy.
* Add code of conduct.
* Add issue templates.
* Add pull-request template.
* Add license.
* Add CI workflows.
* Add project roadmap.
* Add evaluation-results summary.
* Add demo instructions.
* Add architecture decision records.

## README Must Include

* Project problem statement.
* Why the project matters.
* Key features.
* Architecture overview.
* Agent roles.
* MCP integration.
* Security design.
* Evaluation strategy.
* Installation and setup.
* Environment variables.
* Usage example.
* Screenshots or demo.
* Limitations.
* Future roadmap.
* Contribution guidance.
* License.

## Acceptance Criteria

* A new developer can set up and run the project from README instructions.
* Architecture and safety design are explained clearly.
* Evaluation evidence is visible.
* Repository metadata, topics, and social-preview assets are complete.

---

# 15.17 Milestone 14 — Demo Preparation

## Objective

Prepare a short, reliable demonstration that proves the project’s real agentic capabilities.

## Demo Flow

1. Introduce the repository-quality problem.
2. Enter a public repository URL.
3. Show validation and repository discovery.
4. Show agent-analysis progress.
5. Show specialized findings.
6. Show health score and prioritization.
7. Show security redaction.
8. Generate a draft improvement section.
9. Show evaluation results or fixture comparison.
10. Explain safety boundaries and future roadmap.

## Recommended Demo Repositories

| Repository Type              | Demonstrates                                      |
| ---------------------------- | ------------------------------------------------- |
| Weak student project fixture | Documentation and portfolio gaps                  |
| Streamlit ML fixture         | Reproducibility and AI-project recommendations    |
| Security-risk fixture        | Secret redaction and safety controls              |
| High-quality fixture         | Balanced analysis and low false-positive behavior |

## Demo Reliability Plan

* Keep local fixture mode available.
* Maintain screenshots or recorded fallback clips.
* Use a known public repository only after testing it before recording.
* Avoid depending solely on unstable live API access.
* Demonstrate at least one real MCP tool interaction.
* Clearly state that generated recommendations are drafts, not automatic changes.

## Acceptance Criteria

* The demo runs end-to-end in a controlled environment.
* The demo visibly demonstrates multi-agent coordination.
* The demo includes safety behavior.
* The demo avoids untested live dependencies where possible.

---

# 15.18 Recommended Build Order

| Order | Deliverable                                                         | Priority    |
| ----: | ------------------------------------------------------------------- | ----------- |
|     1 | Project scaffold, configuration, CI                                 | Must Have   |
|     2 | Schemas and settings                                                | Must Have   |
|     3 | Repository intake and validation                                    | Must Have   |
|     4 | GitHub MCP adapter and safe file access                             | Must Have   |
|     5 | Deterministic skills and redaction                                  | Must Have   |
|     6 | End-to-end documentation-analysis thin slice                        | Must Have   |
|     7 | Structure, Quality, Security, Discoverability, and Portfolio agents | Must Have   |
|     8 | Coordinator synthesis, prioritization, and health scoring           | Must Have   |
|     9 | Security guardrails and adversarial tests                           | Must Have   |
|    10 | Streamlit dashboard                                                 | Must Have   |
|    11 | Draft content generation                                            | Should Have |
|    12 | Evaluation fixtures and metrics                                     | Must Have   |
|    13 | Documentation, diagrams, and GitHub polish                          | Must Have   |
|    14 | Deployment and demo recording                                       | Must Have   |

---

# 15.19 MVP Feature Cut Line

The following features are required for a complete MVP:

* Public GitHub repository input and validation.
* Read-only GitHub MCP or API integration.
* Repository structure analysis.
* At least five specialist agents.
* Coordinator Agent synthesis.
* Structured findings and prioritized recommendations.
* Repository health score with category breakdown.
* Secret redaction and prompt-injection defense.
* Streamlit interface.
* Fixture-based evaluation.
* Documentation and demo materials.

The following features may be deferred if time is limited:

* Release-history analysis.
* Full issue-template analysis.
* Advanced workflow-content parsing.
* Extensive draft generation types.
* Docker deployment.
* Cloud Run deployment.
* Advanced visual analytics.
* Persistent report history.
* Authentication.
* Private repository access.
* Pull-request generation.

---

# 15.20 Implementation Risks and Mitigations

| Risk                                     | Impact                          | Mitigation                                                            |
| ---------------------------------------- | ------------------------------- | --------------------------------------------------------------------- |
| ADK integration complexity               | Delays multi-agent workflow     | Start with a small Coordinator plus two agents, then expand           |
| GitHub MCP availability or compatibility | Blocks live analysis            | Maintain a direct GitHub adapter with the same tool contracts         |
| API rate limits                          | Unreliable demo                 | Use fixtures, caching, and local fallback mode                        |
| Too many agents create duplication       | Weak report quality             | Normalize and deduplicate findings in deterministic coordinator logic |
| Prompt injection in repositories         | Unsafe agent behavior           | Treat files as untrusted data and enforce guardrails                  |
| Secret leakage                           | Release-blocking security issue | Redaction before agent context, logging, and UI output                |
| Scope expansion                          | Incomplete MVP                  | Follow the feature cut line strictly                                  |
| UI implementation takes too long         | Reduces core-agent quality      | Build UI only after the thin slice works                              |
| Inconsistent scoring                     | Low user trust                  | Use deterministic scoring and documented deduction rules              |
| Weak evaluation evidence                 | Lower competition credibility   | Build fixtures and metrics before final polish                        |

---
