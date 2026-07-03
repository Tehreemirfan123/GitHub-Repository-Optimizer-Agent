# Agent Skills

### 10.1 Purpose

This document defines the reusable Agent Skills used by the GitHub Repository Optimizer Agent.

Skills are focused, reusable capabilities that support agents with deterministic logic, structured reasoning patterns, or controlled content generation. They reduce duplication across agents, improve consistency, and keep domain-specific logic separate from orchestration logic.

In this project, agents are responsible for interpretation and recommendations, while skills provide repeatable capabilities such as validation, technology detection, scoring, redaction, and draft generation.

---

# 10.2 Skill Design Principles

All skills must follow these principles:

1. **Reusable**
   A skill should support more than one agent where possible.

2. **Deterministic where possible**
   Use rule-based logic for tasks such as validation, file classification, scoring, and redaction.

3. **Bounded responsibility**
   Each skill should solve one clear problem.

4. **Schema-based inputs and outputs**
   Skills should accept validated inputs and return structured results.

5. **Safe by default**
   Skills must not expose secrets, execute repository code, or modify GitHub repositories.

6. **Composable**
   Skills should be easy to combine in agent workflows.

7. **Testable independently**
   Each skill should have unit tests separate from full agent evaluations.

---

# 10.3 Skill Inventory

| Skill Name                          | Primary Purpose                                                   | Used By                                             |
| ----------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------- |
| Repository Intake Skill             | Parse and validate repository input                               | Coordinator Agent                                   |
| Repository Classification Skill     | Identify project type and repository category                     | Structure Agent, Coordinator Agent                  |
| Technology Detection Skill          | Detect languages, frameworks, and tools                           | Structure, Documentation, Quality, Portfolio Agents |
| File Prioritization Skill           | Select high-value files for analysis                              | Structure Agent, Coordinator Agent                  |
| Documentation Coverage Skill        | Identify missing README and documentation sections                | Documentation Agent                                 |
| Engineering Hygiene Skill           | Detect maintainability signals and missing practices              | Quality Agent                                       |
| Security Hygiene Skill              | Detect risky files, secret-like patterns, and missing protections | Security Agent                                      |
| Discoverability Skill               | Assess GitHub metadata and community readiness                    | Discoverability Agent                               |
| Portfolio Readiness Skill           | Assess project presentation quality                               | Portfolio Agent                                     |
| Recommendation Normalization Skill  | Standardize findings from all agents                              | Coordinator Agent                                   |
| Recommendation Prioritization Skill | Assign priority and effort levels                                 | Coordinator Agent                                   |
| Repository Health Scoring Skill     | Calculate category and overall health scores                      | Coordinator Agent                                   |
| Secret Redaction Skill              | Remove secret-like values from content and outputs                | Tool Gateway, Security Agent                        |
| Draft Content Generation Skill      | Generate reviewable repository documentation drafts               | Documentation, Discoverability, Portfolio Agents    |
| Limitation Reporting Skill          | Convert unavailable evidence into clear limitations               | All Agents                                          |

---

# 10.4 Repository Intake Skill

## Purpose

The Repository Intake Skill validates and normalizes repository input before analysis begins.

## Inputs

* GitHub repository URL, or
* Repository identifier in `owner/repository` format.

## Responsibilities

* Validate supported input formats.
* Extract owner and repository name.
* Normalize repository URLs.
* Reject malformed or unsupported repository references.
* Confirm that the repository is public and accessible through the MCP adapter.
* Return user-safe validation errors.

## Output Example

```json id="skills01"
{
  "valid": true,
  "owner": "example-owner",
  "repository": "example-repository",
  "normalized_url": "https://github.com/example-owner/example-repository",
  "visibility": "public",
  "message": null
}
```

## Decision Rules

* Only `github.com` repositories are supported in the MVP.
* Private repositories are rejected.
* Repository identifiers must contain exactly one owner and one repository segment.
* The skill must not expose raw GitHub API errors.

---

# 10.5 Repository Classification Skill

## Purpose

The Repository Classification Skill determines the broad category of a repository.

## Supported Categories

* Web application
* Backend API
* Python package
* JavaScript or TypeScript package
* Machine learning project
* Data science project
* Streamlit application
* CLI tool
* Documentation project
* Multi-service application
* General software repository
* Unknown or mixed repository

## Evidence Sources

* Root-level files.
* Directory names.
* Dependency manifests.
* Framework configuration.
* File extensions.
* Deployment files.
* Notebook presence.
* Model or dataset references.

## Example Signals

| Signal                                                 | Possible Classification                    |
| ------------------------------------------------------ | ------------------------------------------ |
| `streamlit_app.py`, `app.py`, `streamlit` dependency   | Streamlit application                      |
| `requirements.txt`, `train.py`, notebooks, model files | Machine learning project                   |
| `package.json`, `src/`, `vite.config.*`                | Web application                            |
| `FastAPI`, `uvicorn`, `main.py`                        | Backend API                                |
| `pyproject.toml`, `src/package_name/`                  | Python package                             |
| `Dockerfile`, `docker-compose.yml`                     | Containerized or multi-service application |

## Output Example

```json id="skills02"
{
  "repository_category": "machine_learning_project",
  "secondary_categories": ["streamlit_application"],
  "confidence": "high",
  "evidence": [
    "requirements.txt includes torch and streamlit",
    "model/ directory contains model checkpoint files",
    "app.py imports streamlit"
  ]
}
```

---

# 10.6 Technology Detection Skill

## Purpose

The Technology Detection Skill identifies programming languages, frameworks, build tools, package managers, testing tools, deployment tools, and data-science libraries.

## Responsibilities

* Detect programming languages through file extensions and manifests.
* Detect frameworks through dependency files and configuration.
* Identify package managers and build tools.
* Identify testing, formatting, linting, and type-checking tools.
* Identify deployment and containerization tools.
* Distinguish confirmed signals from inferred signals.

## Example Output

```json id="skills03"
{
  "languages": [
    {
      "name": "Python",
      "confidence": "high",
      "evidence": ["requirements.txt", ".py files"]
    }
  ],
  "frameworks": [
    {
      "name": "Streamlit",
      "confidence": "high",
      "evidence": ["streamlit dependency", "app.py import"]
    }
  ],
  "tools": [
    {
      "name": "PyTorch",
      "confidence": "high",
      "evidence": ["torch dependency"]
    }
  ]
}
```

## Design Rule

The skill must never claim a technology is used unless it has repository evidence. For example, a project with a `Dockerfile` may be containerized, but the skill should not claim Kubernetes deployment unless Kubernetes configuration files are present.

---

# 10.7 File Prioritization Skill

## Purpose

The File Prioritization Skill selects a small, high-value subset of repository files for inspection.

This is essential for cost efficiency, privacy protection, performance, and reliable agent behavior.

## Priority Tiers

| Tier   | File Type                     | Examples                                                                        |
| ------ | ----------------------------- | ------------------------------------------------------------------------------- |
| Tier 1 | Repository identity and setup | `README.md`, `package.json`, `requirements.txt`, `pyproject.toml`, `Dockerfile` |
| Tier 2 | Quality and automation        | `.github/workflows/*`, test config, lint config, formatter config               |
| Tier 3 | Security and configuration    | `.gitignore`, `.env.example`, `SECURITY.md`, deployment config                  |
| Tier 4 | Documentation and community   | `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, issue templates, changelog             |
| Tier 5 | Contextual evidence           | Entry-point files, evaluation summaries, architecture docs                      |

## Excluded Files

The skill should exclude or deprioritize:

* Binary files
* Large datasets
* Model checkpoints
* Images and videos
* Archives
* Generated output directories
* Dependency folders such as `node_modules/`
* Virtual environments
* Build artifacts
* Cache directories
* Files exceeding configured size limits

## Output Example

```json id="skills04"
{
  "selected_files": [
    "README.md",
    "requirements.txt",
    ".gitignore",
    "app.py",
    ".github/workflows/tests.yml"
  ],
  "excluded_paths": [
    "node_modules/",
    ".venv/",
    "model/model.pth"
  ],
  "selection_rationale": [
    "README.md is needed for documentation and portfolio assessment.",
    "requirements.txt is needed for technology and reproducibility analysis.",
    ".gitignore is needed for security hygiene review."
  ]
}
```

---

# 10.8 Documentation Coverage Skill

## Purpose

The Documentation Coverage Skill evaluates whether a repository provides essential documentation for users, contributors, and maintainers.

## Documentation Checklist

The skill checks for evidence of:

* Project title and purpose.
* Problem statement.
* Feature overview.
* Installation instructions.
* Prerequisites.
* Configuration and environment variables.
* Usage instructions.
* Example commands or screenshots.
* Testing instructions.
* Deployment instructions.
* Architecture overview.
* Contribution guidance.
* License information.
* Troubleshooting notes.
* Limitations.
* Contact or support guidance.

## Output Example

```json id="skills05"
{
  "coverage": {
    "project_overview": true,
    "installation": false,
    "configuration": false,
    "usage": true,
    "testing": false,
    "deployment": false,
    "architecture": false,
    "contributing": false,
    "license": true
  },
  "missing_sections": [
    "installation",
    "configuration",
    "testing",
    "architecture",
    "contributing"
  ],
  "confidence": "high"
}
```

## Use Rule

The skill should identify whether a section is present, not whether it is high quality by itself. Quality interpretation remains the Documentation Agent’s responsibility.

---

# 10.9 Engineering Hygiene Skill

## Purpose

The Engineering Hygiene Skill checks repository-level signals related to maintainability, repeatability, and software-engineering maturity.

## Assessment Areas

* Tests and test configuration.
* CI/CD workflow presence.
* Linting configuration.
* Formatting configuration.
* Type-checking configuration.
* Dependency pinning.
* Configuration separation.
* Environment templates.
* Changelog or release notes.
* Clear project entry points.
* Source-code organization.
* Deployment configuration.

## Example Output

```json id="skills06"
{
  "signals": {
    "tests_present": false,
    "ci_present": false,
    "linting_present": false,
    "formatting_present": false,
    "type_checking_present": false,
    "dependency_manifest_present": true,
    "dependency_versions_pinned": "partial",
    "environment_example_present": false
  },
  "missing_practices": [
    "automated_tests",
    "continuous_integration",
    "environment_template"
  ]
}
```

## Design Rule

The skill checks observable signals only. It must not claim that code quality is poor simply because a linter configuration is absent.

---

# 10.10 Security Hygiene Skill

## Purpose

The Security Hygiene Skill performs deterministic repository-level safety checks before the Security Agent interprets risks.

## Responsibilities

* Identify potentially sensitive paths.
* Detect tracked environment files.
* Scan approved text files for secret-like patterns.
* Check `.gitignore` coverage.
* Check for security-policy files.
* Flag unsafe configuration indicators.
* Redact sensitive-looking values.

## Example Checks

| Check                    | Example                                                     |
| ------------------------ | ----------------------------------------------------------- |
| Tracked environment file | `.env` exists in repository tree                            |
| Missing ignore rule      | `.gitignore` lacks `.env` or `*.pem` patterns               |
| Token-like pattern       | Strings resembling API keys, bearer tokens, or private keys |
| Sensitive configuration  | Database URLs, cloud credentials, production endpoints      |
| Missing policy           | `SECURITY.md` is absent from public repository              |

## Output Example

```json id="skills07"
{
  "risk_indicators": [
    {
      "type": "tracked_environment_file",
      "path": ".env",
      "confidence": "high",
      "redacted_summary": "A tracked environment file was detected."
    }
  ],
  "redactions_applied": 2,
  "security_policy_present": false,
  "gitignore_coverage": "insufficient"
}
```

## Safety Rule

The skill must redact sensitive values before returning results. It must never expose a token, password, private key, full connection string, or credential value.

---

# 10.11 Discoverability Skill

## Purpose

The Discoverability Skill evaluates repository metadata and public GitHub signals that affect trust, visibility, and contributor onboarding.

## Assessment Areas

* Repository description.
* Topics.
* License.
* README presence.
* Contribution guidance.
* Code of conduct.
* Security policy.
* Issue templates.
* Pull-request templates.
* Release history.
* Changelog.
* Support guidance.

## Example Output

```json id="skills08"
{
  "signals": {
    "repository_description_present": true,
    "topics_present": false,
    "license_present": false,
    "contributing_guide_present": false,
    "issue_templates_present": false,
    "pull_request_template_present": false,
    "code_of_conduct_present": false,
    "releases_present": false
  },
  "recommended_improvements": [
    "Add a concise repository description.",
    "Add relevant GitHub topics.",
    "Add an open-source license.",
    "Add contribution guidance."
  ]
}
```

---

# 10.12 Portfolio Readiness Skill

## Purpose

The Portfolio Readiness Skill identifies evidence that a repository demonstrates meaningful technical work to recruiters, clients, and collaborators.

## Assessment Areas

* Clear project purpose.
* Problem and user value.
* Technology stack explanation.
* Architecture explanation.
* Screenshots, diagrams, demo links, or videos.
* Deployment instructions.
* Benchmarks, results, or metrics.
* Reproducibility instructions.
* Project highlights.
* Roadmap or future improvements.
* Limitations and trade-offs.

## Example Output

```json id="skills09"
{
  "portfolio_signals": {
    "problem_statement_present": false,
    "technology_stack_present": true,
    "architecture_explanation_present": false,
    "screenshots_or_demo_present": false,
    "results_or_metrics_present": true,
    "deployment_guidance_present": false,
    "project_highlights_present": false
  },
  "presentation_gaps": [
    "problem_statement",
    "architecture_diagram",
    "demo_or_screenshots",
    "deployment_guidance"
  ]
}
```

---

# 10.13 Recommendation Normalization Skill

## Purpose

The Recommendation Normalization Skill converts agent-specific findings into a shared format before prioritization and report generation.

## Responsibilities

* Apply consistent field names.
* Normalize category labels.
* Validate priority and confidence values.
* Ensure evidence is present.
* Remove malformed findings.
* Add agent source metadata.
* Mark findings requiring human review.

## Example Normalized Finding

```json id="skills10"
{
  "finding_id": "QUAL-004",
  "agent": "quality_agent",
  "category": "maintainability",
  "title": "No automated test configuration detected",
  "priority": "medium",
  "confidence": "high",
  "evidence": [
    {
      "type": "repository_structure",
      "path": "tests/",
      "summary": "No test directory or test framework configuration was found."
    }
  ],
  "why_it_matters": "Regression risks increase when changes cannot be validated automatically.",
  "recommended_action": "Add a basic test suite and configure a test command.",
  "suggested_location": "tests/ and project configuration",
  "estimated_effort": "medium",
  "requires_human_review": true
}
```

---

# 10.14 Recommendation Prioritization Skill

## Purpose

The Recommendation Prioritization Skill assigns consistent priority and effort levels to normalized findings.

## Priority Inputs

* Security severity.
* User impact.
* Project type.
* Repository visibility.
* Reproducibility impact.
* Maintainability impact.
* Discoverability impact.
* Evidence confidence.
* Implementation effort.
* Dependency on other recommendations.

## Example Rules

| Condition                                          | Suggested Priority |
| -------------------------------------------------- | ------------------ |
| Confirmed exposed credential                       | Critical           |
| Public repository has no README                    | High               |
| README lacks installation instructions             | High               |
| No `.gitignore` for environment files              | High               |
| No test suite for a production-style application   | Medium or High     |
| No GitHub topics                                   | Low or Medium      |
| No screenshots for a user-facing portfolio project | Medium             |
| No badges                                          | Low                |

## Output Example

```json id="skills11"
{
  "priority": "high",
  "estimated_effort": "small",
  "priority_reason": "The missing installation section prevents users from reproducing the project and affects usability immediately."
}
```

---

# 10.15 Repository Health Scoring Skill

## Purpose

The Repository Health Scoring Skill produces category-level and overall heuristic scores based on normalized findings.

## Categories

* Documentation
* Maintainability
* Security hygiene
* Discoverability
* Portfolio readiness
* Developer experience

## Scoring Method

Each category begins with a baseline score of 100.

Points are deducted based on validated findings. Deductions depend on:

* Priority.
* Confidence.
* Category relevance.
* Repository type.
* Whether the issue blocks setup, trust, or safe use.

Example deduction ranges:

| Finding Priority | Suggested Deduction Range |
| ---------------- | ------------------------: |
| Critical         |              25–40 points |
| High             |              12–25 points |
| Medium           |               5–12 points |
| Low              |                1–5 points |

The final score is clamped between 0 and 100.

## Important Rule

Scores are heuristic indicators, not objective measurements of code quality or security.

## Output Example

```json id="skills12"
{
  "overall_score": 68,
  "category_scores": {
    "documentation": 55,
    "maintainability": 62,
    "security_hygiene": 78,
    "discoverability": 60,
    "portfolio_readiness": 70,
    "developer_experience": 65
  },
  "scoring_notes": [
    "Scores are heuristic and based on inspected repository evidence.",
    "Repository content was selectively sampled.",
    "Security hygiene score is not a substitute for a professional security audit."
  ]
}
```

---

# 10.16 Secret Redaction Skill

## Purpose

The Secret Redaction Skill removes sensitive-looking values from retrieved repository content, logs, agent context, findings, and user-facing reports.

## Supported Redaction Targets

* API keys.
* Access tokens.
* Private keys.
* Passwords.
* Bearer tokens.
* Database credentials.
* Cloud provider credentials.
* JWT-like strings.
* Secret environment-variable assignments.
* Full connection strings.

## Example Redaction

```text id="skills13"
Before:
OPENAI_API_KEY=sk-proj-example-secret-value

After:
OPENAI_API_KEY=[REDACTED]
```

## Output Rules

* Preserve enough context to explain the finding safely.
* Never retain raw secret values in agent state.
* Never display raw secret values in logs or reports.
* Mark when redaction has been applied.
* Fail closed if redaction cannot be guaranteed.

---

# 10.17 Draft Content Generation Skill

## Purpose

The Draft Content Generation Skill creates reviewable, repository-specific drafts for common missing or weak artifacts.

## Supported Draft Types

* README sections.
* `CONTRIBUTING.md`.
* `SECURITY.md`.
* `.env.example`.
* `.gitignore` additions.
* Issue templates.
* Pull-request templates.
* `CODE_OF_CONDUCT.md`.
* Architecture overview sections.
* Dataset, training, and evaluation documentation for AI projects.

## Input Requirements

The skill requires:

* Repository type.
* Technology stack.
* Relevant repository evidence.
* Selected recommendation.
* Target file or section.
* Explicit user request.

## Output Requirements

Generated drafts must:

* Be clearly labeled as drafts.
* Avoid unsupported claims.
* Avoid sensitive values.
* Use repository-specific details only when supported by evidence.
* Explain where the content should be placed.
* Require user review before use.

## Example Output

```json id="skills14"
{
  "target_file": "README.md",
  "target_section": "Installation",
  "draft_status": "review_required",
  "content": "## Installation\n\n1. Clone the repository...\n",
  "placement_guidance": "Add this section after the project overview and before Usage."
}
```

---

# 10.18 Limitation Reporting Skill

## Purpose

The Limitation Reporting Skill ensures that missing data, tool failures, repository truncation, and unsupported repository features are described consistently.

## Example Limitations

* “The repository tree was truncated after 500 entries; findings are based on the inspected subset.”
* “Workflow files were detected but could not be retrieved within configured limits.”
* “Security review was limited to approved text configuration files.”
* “No conclusion can be drawn about uninspected binary, generated, or oversized files.”
* “The repository appears to be an AI project, but no evaluation metrics were found in the inspected files.”

## Output Example

```json id="skills15"
{
  "limitations": [
    {
      "category": "repository_scope",
      "message": "Analysis was limited to 25 selected text files."
    },
    {
      "category": "security",
      "message": "Security hygiene review does not replace a full vulnerability assessment."
    }
  ]
}
```

---

# 10.19 Skill Collaboration Flow

```text id="skills16"
Repository Input
      ↓
Repository Intake Skill
      ↓
Repository Classification + Technology Detection Skills
      ↓
File Prioritization Skill
      ↓
Specialist Agent Analysis
      ↓
Security Hygiene + Secret Redaction Skills
      ↓
Recommendation Normalization Skill
      ↓
Recommendation Prioritization Skill
      ↓
Repository Health Scoring Skill
      ↓
Limitation Reporting Skill
      ↓
Final Report
      ↓
Optional Draft Content Generation Skill
```

---
