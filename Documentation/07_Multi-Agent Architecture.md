# Multi-Agent Architecture

### 7.1 Purpose

This document defines the multi-agent design of the GitHub Repository Optimizer Agent.

The system is designed as a coordinated team of specialized agents rather than a single general-purpose chatbot. Each agent is responsible for a narrow repository-quality domain and returns structured findings. A central Coordinator Agent combines those findings into one evidence-based optimization report.

This approach improves clarity, maintainability, traceability, evaluation, and safety.

---

# 7.2 Why a Multi-Agent Design Is Used

Repository optimization requires several different forms of expertise:

* Repository structure analysis
* Documentation review
* Engineering quality assessment
* Security hygiene review
* GitHub discoverability assessment
* Portfolio presentation assessment
* Recommendation prioritization

A single agent can attempt all these tasks, but it is more likely to produce duplicate recommendations, inconsistent reasoning, incomplete coverage, and weak traceability.

The multi-agent design allows each specialist to focus on a defined responsibility while the Coordinator Agent ensures that the final output remains coherent.

---

# 7.3 Agent Topology

```text id="multiagent01"
                         ┌───────────────────────────┐
                         │        User Request       │
                         │ GitHub URL / owner-repo   │
                         └─────────────┬─────────────┘
                                       │
                                       ▼
                         ┌───────────────────────────┐
                         │      Intake Controller    │
                         │ Validation + Session Init │
                         └─────────────┬─────────────┘
                                       │
                                       ▼
                         ┌───────────────────────────┐
                         │     Coordinator Agent     │
                         │ Plan, Delegate, Synthesize│
                         └───────┬───────┬───────────┘
                                 │       │
         ┌───────────────────────┼───────┼────────────────────────┐
         │                       │       │                        │
         ▼                       ▼       ▼                        ▼
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐  ┌─────────────────┐
│ Repository      │   │ Documentation   │   │ Quality Agent   │  │ Security Agent  │
│ Structure Agent │   │ Agent           │   │                 │  │                 │
└────────┬────────┘   └────────┬────────┘   └────────┬────────┘  └────────┬────────┘
         │                     │                     │                    │
         ▼                     ▼                     ▼                    ▼
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐  ┌─────────────────┐
│ Discoverability │   │ Portfolio Agent │   │ Scoring Engine  │  │ Guardrail Layer │
│ Agent           │   │                 │   │                 │  │                 │
└────────┬────────┘   └────────┬────────┘   └────────┬────────┘  └────────┬────────┘
         │                     │                     │                    │
         └─────────────────────┴──────────────┬──────┴────────────────────┘
                                              │
                                              ▼
                              ┌────────────────────────────┐
                              │ Final Optimization Report  │
                              │ Findings + Score + Drafts  │
                              └────────────────────────────┘
```

---

# 7.4 Agent Design Principles

All agents must follow these principles:

1. **Single responsibility**
   Each agent should focus on one repository-quality domain.

2. **Structured output only**
   Agents should return schemas, not uncontrolled narrative responses.

3. **Evidence-based findings**
   Every recommendation should reference repository evidence, missing artifacts, metadata, or clearly stated assumptions.

4. **Least-privilege tool access**
   Agents receive access only to tools and files needed for their role.

5. **No repository modification**
   Agents may recommend or draft changes but cannot commit, push, delete, or change repository settings.

6. **Confidence-aware reasoning**
   Agents must distinguish between confirmed evidence, likely inference, and unknown information.

7. **Safe failure behavior**
   If evidence is unavailable, agents must report a limitation instead of guessing.

8. **Human review requirement**
   Generated content and recommendations remain drafts for user review.

---

# 7.5 Shared Agent Input Contract

Every specialist agent receives a limited repository context package.

```json id="multiagent02"
{
  "repository": {
    "owner": "example-owner",
    "name": "example-repository",
    "url": "https://github.com/example-owner/example-repository",
    "description": "Repository description",
    "primary_language": "Python",
    "topics": ["machine-learning", "streamlit"],
    "license": "MIT",
    "default_branch": "main"
  },
  "structure_summary": {
    "root_files": [],
    "directories": [],
    "important_files": [],
    "missing_artifacts": []
  },
  "selected_files": [
    {
      "path": "README.md",
      "content": "Sanitized and size-limited file content"
    }
  ],
  "technology_signals": [],
  "analysis_limits": {
    "max_file_size_bytes": 200000,
    "max_total_content_bytes": 1000000,
    "sampled_repository": false
  }
}
```

The context package must be sanitized before specialist agents receive it. Sensitive-looking values must be redacted, oversized files excluded, and irrelevant content omitted.

---

# 7.6 Shared Agent Output Contract

All specialist agents return the same high-level structure.

```json id="multiagent03"
{
  "agent_name": "documentation_agent",
  "status": "completed",
  "summary": "Documentation is present but lacks installation and testing instructions.",
  "findings": [
    {
      "finding_id": "DOC-001",
      "title": "Missing installation instructions",
      "category": "documentation",
      "priority": "high",
      "confidence": "high",
      "evidence": [
        {
          "type": "file",
          "path": "README.md",
          "summary": "README describes the project but has no installation commands."
        }
      ],
      "why_it_matters": "New users cannot reliably set up the application.",
      "recommended_action": "Add prerequisites, installation commands, and a verification step.",
      "suggested_location": "README.md > Installation",
      "estimated_effort": "small",
      "requires_human_review": true
    }
  ],
  "limitations": [],
  "confidence": "high"
}
```

---

# 7.7 Coordinator Agent

## Purpose

The Coordinator Agent is the control plane of the multi-agent system.

It does not perform every analysis itself. Instead, it decides which specialist agents should run, provides bounded context, collects results, resolves overlap, prioritizes recommendations, and produces the final report.

## Responsibilities

The Coordinator Agent shall:

* Confirm that repository validation has succeeded.
* Read the repository structure summary and detected technology stack.
* Determine which specialist agents are relevant.
* Delegate scoped tasks with defined inputs.
* Monitor specialist agent status.
* Request clarification or additional evidence through approved tools when necessary.
* Deduplicate findings.
* Resolve conflicts between agents.
* Rank recommendations by priority, impact, effort, confidence, and user value.
* Generate the repository health score.
* Produce a coherent final report.
* Identify incomplete analysis areas.

## Inputs

* Validated repository metadata
* Repository structure summary
* Technology-stack signals
* Sanitized file content
* Specialist-agent results
* Guardrail results
* Analysis limits and tool status

## Outputs

* Analysis plan
* Delegation plan
* Consolidated findings
* Prioritized recommendations
* Repository health score
* Final report
* Limitations and disclaimers

## Decision Boundaries

The Coordinator Agent must not:

* Retrieve arbitrary repository files directly without the Tool Gateway.
* Override security redaction controls.
* Treat an agent failure as evidence of repository quality.
* Generate unsupported claims.
* Modify GitHub repositories.
* Expose raw tool results or internal agent reasoning.

---

# 7.8 Repository Structure Agent

## Purpose

The Repository Structure Agent creates the repository inventory and determines which files should be inspected by other agents.

It is the first specialist agent in the workflow because all later agents rely on its repository map.

## Responsibilities

* Analyze root-level files and major directories.
* Identify source, tests, documentation, workflows, configuration, deployment, and package files.
* Detect generated folders, vendor folders, binaries, and large artifacts.
* Infer likely repository type.
* Identify high-value files for deeper review.
* Identify missing baseline artifacts.
* Produce a bounded file-access plan.

## Typical Inputs

* Repository metadata
* File tree
* Root file names
* File sizes
* File extensions
* Directory names

## Typical Outputs

* Repository layout summary
* Important file inventory
* Candidate files for other agents
* Missing baseline artifacts
* Technology signals
* Sampling limitations

## Example Findings

* `README.md` is missing.
* `tests/` directory is not present.
* `package.json` indicates a React application.
* `.github/workflows/` is absent.
* Large model checkpoint files are committed.
* `node_modules/` appears to be tracked and should be excluded.

## Tool Access

* Get repository metadata
* Get repository file tree
* Get limited root-level text files
* Get selected manifest files

The agent must not retrieve full repository contents or execute repository code.

---

# 7.9 Documentation Agent

## Purpose

The Documentation Agent evaluates whether users, contributors, developers, and recruiters can understand, install, run, and maintain the repository.

## Responsibilities

* Review README quality and completeness.
* Identify missing documentation sections.
* Review setup, configuration, usage, testing, and deployment instructions.
* Check for architecture, contribution, troubleshooting, and limitation documentation.
* Detect missing supporting documentation files.
* Recommend documentation improvements.
* Prepare draft content requirements for the content-generation skill.

## Typical Inputs

* README content
* Documentation folder content
* Package manifests
* Deployment configuration
* Test configuration
* Repository structure summary
* Technology-stack signals

## Typical Outputs

* Documentation coverage assessment
* Missing and weak sections
* Documentation recommendations
* Suggested document templates
* Draft-generation opportunities

## Example Findings

* README does not contain installation instructions.
* Environment variables are referenced but not documented.
* Tests are present but no test command is documented.
* Deployment workflow exists but deployment steps are missing.
* No architecture explanation exists for a multi-agent application.

## Tool Access

* Read README
* Read selected documentation files
* Read package and configuration files
* Read test configuration metadata

The agent must not inspect arbitrary source files unless the Coordinator Agent explicitly requests limited contextual evidence.

---

# 7.10 Quality Agent

## Purpose

The Quality Agent evaluates repository-level maintainability and engineering-practice signals.

## Responsibilities

* Check for test directories and test configuration.
* Identify CI/CD workflow files.
* Check for linting, formatting, and type-checking configuration.
* Evaluate dependency-management signals.
* Review project structure consistency.
* Check for environment configuration practices.
* Identify missing engineering hygiene artifacts.

## Typical Inputs

* Repository structure summary
* Dependency manifests
* CI workflow files
* Test configuration files
* Linter and formatter configuration
* Environment example files

## Typical Outputs

* Maintainability findings
* Engineering hygiene findings
* Recommendations for tests, CI, linting, formatting, or structure
* Confidence score
* Limitations

## Example Findings

* No test directory or test configuration was found.
* GitHub Actions workflow is absent.
* `requirements.txt` exists but dependency versions are unpinned.
* `.env.example` is missing while environment variables are used.
* Project has source files at the root, reducing maintainability.

## Tool Access

* Read package manifests
* Read CI workflow metadata
* Read test configuration
* Read linting and formatting configuration
* Read selected environment templates

The agent must not claim code coverage percentages unless they are explicitly present in repository evidence.

---

# 7.11 Security Agent

## Purpose

The Security Agent performs lightweight repository-security hygiene checks while enforcing strict redaction and safe reporting.

## Responsibilities

* Identify high-risk files and paths.
* Check for possible committed environment files.
* Detect secret-like patterns in approved files.
* Check `.gitignore` coverage for common sensitive files.
* Check for `SECURITY.md`.
* Detect potentially unsafe configuration patterns.
* Classify findings by confidence and severity.
* Redact sensitive values before output.

## Risk Classification

| Classification     | Meaning                                                                                    |
| ------------------ | ------------------------------------------------------------------------------------------ |
| Confirmed Exposure | A credential-like value appears to be committed and requires immediate rotation or removal |
| Suspicious Pattern | A value resembles a secret but requires human review                                       |
| Manual Review      | Repository evidence suggests a potential security hygiene gap without proving exposure     |

## Typical Inputs

* `.gitignore`
* `.env.example`
* Selected configuration files
* README environment-variable instructions
* Security policy files
* File-path metadata
* Sanitized snippets from high-risk files

## Typical Outputs

* Redacted findings
* Risk classification
* File-path references
* Recommended remediation
* Security limitations statement

## Example Findings

* `.env` file is tracked in the repository.
* `.gitignore` does not exclude `.env` files.
* A token-like pattern was detected in a configuration file and has been redacted.
* `SECURITY.md` is missing from a public repository.
* A production endpoint is hard-coded and should be moved to configuration.

## Tool Access

* Read approved configuration files
* Read `.gitignore`
* Read security policy files
* Scan selected text snippets through the redaction layer

The Security Agent must never output full secret values, raw credentials, or sensitive configuration content.

---

# 7.12 Discoverability Agent

## Purpose

The Discoverability Agent evaluates whether the repository is easy to find, trust, understand, and contribute to on GitHub.

## Responsibilities

* Review repository description and topics.
* Check for license presence.
* Check for community-health files.
* Check for issue templates and pull-request templates.
* Review release and changelog signals.
* Identify missing GitHub ecosystem metadata.
* Recommend improvements to public repository presentation.

## Typical Inputs

* Repository metadata
* Topics
* License metadata
* `.github/` directory structure
* README content
* Release metadata
* Changelog files
* Contribution files

## Typical Outputs

* Discoverability score
* Community-readiness findings
* Metadata improvement recommendations
* Missing community-health files
* Suggested topics or description improvements

## Example Findings

* Repository description is empty.
* No GitHub topics are configured.
* License is missing.
* `CONTRIBUTING.md` and issue templates are absent.
* No release tags or changelog are visible.
* README lacks badges or a quick-start section.

## Tool Access

* Read repository metadata
* Read `.github/` directory metadata
* Read README
* Read license and contribution files
* Read release metadata

---

# 7.13 Portfolio Readiness Agent

## Purpose

The Portfolio Readiness Agent evaluates whether the repository demonstrates the developer’s work effectively to recruiters, clients, collaborators, and evaluators.

## Responsibilities

* Assess project purpose and user value explanation.
* Check for screenshots, demos, diagrams, or video links.
* Check for architecture explanations.
* Check for measurable results, benchmarks, or evaluation metrics.
* Evaluate reproducibility and onboarding clarity.
* Identify presentation gaps.
* Recommend portfolio-oriented improvements.

## Typical Inputs

* README content
* Documentation files
* Repository metadata
* Demo links
* Image and media directories
* Benchmark or evaluation files
* Architecture documentation
* Technology-stack signals

## Typical Outputs

* Portfolio readiness score
* Presentation strengths
* Presentation gaps
* Recommended README or documentation improvements
* Suggested visual or demo assets

## Example Findings

* The project purpose is unclear in the README.
* No screenshots or demo link are present for a user-facing application.
* The AI model is described but accuracy metrics are missing.
* The repository lacks an architecture diagram.
* There is no “Results” or “Project Highlights” section.

## Tool Access

* Read README and selected docs
* Inspect repository metadata
* Inspect media file names and links
* Read benchmark and evaluation summaries

The agent must not claim performance results unless metrics are visible in the repository.

---

# 7.14 Recommendation Prioritization and Scoring Component

The prioritization and scoring component may be implemented as a dedicated skill called by the Coordinator Agent rather than as a separate autonomous agent.

## Responsibilities

* Normalize recommendations from all agents.
* Map findings to categories.
* Assign consistent priority labels.
* Estimate implementation effort.
* Calculate category-level health scores.
* Calculate an overall heuristic repository health score.
* Preserve confidence levels and evidence references.

## Prioritization Factors

| Factor      | Description                                                     |
| ----------- | --------------------------------------------------------------- |
| Severity    | Potential consequence if the issue remains unresolved           |
| User Impact | Effect on setup, trust, usability, maintainability, or security |
| Effort      | Estimated work required to address the issue                    |
| Confidence  | Strength of repository evidence                                 |
| Context     | Relevance to detected repository type and user persona          |
| Dependency  | Whether one improvement enables several others                  |

## Priority Rules

* Confirmed secret exposure is always Critical.
* Missing README is High for public repositories.
* Missing license is High for repositories intended for reuse or open source.
* Missing tests or CI is normally Medium or High depending on project type.
* Missing screenshots are Medium or Low unless the project is portfolio-focused.
* Cosmetic recommendations remain Low.

---

# 7.15 Agent Collaboration Workflow

```text id="multiagent04"
1. Intake Controller validates repository input
        ↓
2. Repository Structure Agent maps repository and selects high-value files
        ↓
3. Coordinator Agent chooses relevant specialist agents
        ↓
4. Documentation, Quality, Security, Discoverability, and Portfolio Agents run
        ↓
5. Guardrail Layer redacts sensitive-looking values
        ↓
6. Agents return structured findings with evidence and confidence
        ↓
7. Coordinator Agent deduplicates and resolves conflicts
        ↓
8. Prioritization Skill assigns consistent priority and effort values
        ↓
9. Scoring Skill calculates category and overall heuristic scores
        ↓
10. Coordinator Agent produces the final optimization report
```

---

# 7.16 Example Collaboration Scenario

For a Streamlit machine-learning repository:

1. The Repository Structure Agent finds:

   * `app.py`
   * `requirements.txt`
   * `model/model.pth`
   * `README.md`
   * no `tests/`
   * no `.env.example`
   * no `.github/workflows/`

2. The Documentation Agent finds:

   * README explains the project but does not show installation commands.
   * No model download or inference instructions are present.
   * No explanation of the dataset or validation accuracy is included.

3. The Quality Agent finds:

   * No tests or CI workflow.
   * Dependency versions are incomplete.
   * No formatter or linter configuration is visible.

4. The Security Agent finds:

   * `.gitignore` does not exclude `.env`.
   * No confirmed secret exposure.
   * No `SECURITY.md`.

5. The Discoverability Agent finds:

   * Repository has no topics.
   * No license is present.
   * No issue templates or contribution guide are present.

6. The Portfolio Readiness Agent finds:

   * No screenshots or demo GIF.
   * No architecture diagram.
   * Model metrics are missing from the README.

7. The Coordinator Agent produces a prioritized plan:

   * High: Add reproducible setup and inference instructions.
   * High: Add `.gitignore` coverage and `.env.example`.
   * High: Add project results and model evaluation metrics.
   * Medium: Add tests and GitHub Actions workflow.
   * Medium: Add license, topics, and contributor guidance.
   * Low: Add screenshot, architecture diagram, and badges.

---

# 7.17 Agent Failure and Recovery Rules

| Situation                   | Expected Behavior                                                        |
| --------------------------- | ------------------------------------------------------------------------ |
| Structure Agent fails       | Stop specialist analysis because file context is unavailable             |
| Documentation Agent fails   | Continue with other agents and mark documentation review incomplete      |
| Quality Agent fails         | Continue with other agents and mark quality review incomplete            |
| Security Agent fails        | Do not claim repository security status; mark security review incomplete |
| Discoverability Agent fails | Continue and mark discoverability review incomplete                      |
| Portfolio Agent fails       | Continue and mark portfolio assessment incomplete                        |
| One tool call fails         | Retry where appropriate; otherwise continue with available evidence      |
| Redaction layer fails       | Block potentially sensitive output and return a safe limitation message  |

---
