# System Architecture

### 6.1 Purpose

This document defines the high-level system architecture for the GitHub Repository Optimizer Agent.

The architecture is designed to support safe public-repository analysis, multi-agent collaboration, MCP-based GitHub integration, structured outputs, evaluation, and future extensibility. It separates user interaction, orchestration, repository access, analysis, guardrails, and reporting so that each part can evolve independently.

The capstone MVP is intentionally designed as an **analysis-first, human-in-the-loop system**. It can inspect public repositories and generate recommendations, but it does not modify repositories, create pull requests, or perform destructive actions.

---

# 6.2 Architectural Goals

The architecture must support the following goals:

1. **Clear separation of responsibilities**
   User interface, orchestration, repository access, agent analysis, security controls, and report generation should remain separate.

2. **Safe tool access**
   Agents should access GitHub only through controlled tools or MCP server capabilities.

3. **Multi-agent specialization**
   Each agent should focus on one assessment domain instead of attempting unrestricted repository analysis.

4. **Structured and traceable outputs**
   Findings should follow consistent schemas and retain evidence references.

5. **Human review by default**
   Recommendations and generated documentation remain drafts until the user applies them manually.

6. **Scalable repository inspection**
   The system should prioritize high-value files and avoid exhaustive analysis of large repositories.

7. **Production-oriented reliability**
   Failures in one non-critical agent should not prevent the system from returning useful partial results.

---

# 6.3 High-Level Architecture

```text id="arch01"
┌──────────────────────────────────────────────────────────────┐
│                        Presentation Layer                    │
│                                                              │
│   Web UI / Streamlit UI / API Client                         │
│   - Repository URL input                                     │
│   - Analysis progress                                        │
│   - Health score dashboard                                   │
│   - Findings and recommendations                             │
│   - Draft content review                                     │
└───────────────────────────────┬──────────────────────────────┘
                                │
                                ▼
┌──────────────────────────────────────────────────────────────┐
│                     Application Service Layer                │
│                                                              │
│   Analysis API / Session Controller                           │
│   - Validates requests                                        │
│   - Creates analysis session                                  │
│   - Enforces limits                                           │
│   - Returns structured reports                                │
└───────────────────────────────┬──────────────────────────────┘
                                │
                                ▼
┌──────────────────────────────────────────────────────────────┐
│                    Multi-Agent Orchestration Layer           │
│                                                              │
│   Coordinator Agent                                           │
│   - Plans analysis                                            │
│   - Delegates tasks                                           │
│   - Collects findings                                         │
│   - Resolves duplicates                                       │
│   - Prioritizes recommendations                               │
│                                                              │
│   Specialist Agents                                           │
│   - Repository Structure Agent                                │
│   - Documentation Agent                                       │
│   - Quality Agent                                             │
│   - Security Agent                                            │
│   - Discoverability Agent                                     │
│   - Portfolio Readiness Agent                                 │
└───────────────────────────────┬──────────────────────────────┘
                                │
                                ▼
┌──────────────────────────────────────────────────────────────┐
│                    Skills and Tool Access Layer              │
│                                                              │
│   Agent Skills                                                │
│   - Repository intake skill                                   │
│   - Technology detection skill                                │
│   - Documentation assessment skill                            │
│   - Security hygiene skill                                    │
│   - Recommendation prioritization skill                       │
│   - Draft content generation skill                            │
│                                                              │
│   Controlled Tool Gateway                                     │
│   - Input validation                                          │
│   - File allowlists                                           │
│   - Size limits                                               │
│   - Rate-limit handling                                       │
│   - Secret redaction                                          │
└───────────────────────────────┬──────────────────────────────┘
                                │
                                ▼
┌──────────────────────────────────────────────────────────────┐
│                    External Integration Layer                │
│                                                              │
│   GitHub MCP Server / GitHub API                              │
│   - Repository metadata                                       │
│   - File tree                                                 │
│   - Approved file content                                     │
│   - Topics, license, releases, workflows                      │
│                                                              │
│   Optional Future Integrations                                │
│   - Dependency scanners                                       │
│   - GitHub Actions                                            │
│   - GitHub App                                                │
│   - Issue and pull-request APIs                               │
└──────────────────────────────────────────────────────────────┘
```

---

# 6.4 Architectural Layers

## 6.4.1 Presentation Layer

The Presentation Layer is the user-facing interface.

For the capstone MVP, this may be implemented as a Streamlit application or a lightweight web frontend. Its primary purpose is to collect repository input and present results clearly.

### Responsibilities

* Accept a GitHub repository URL or `owner/repository` identifier.
* Display validation errors.
* Show analysis progress and agent status.
* Display repository metadata and detected technology stack.
* Display repository health score and category scores.
* Present findings and prioritized recommendations.
* Show generated draft content in reviewable form.
* Communicate limitations and disclaimers clearly.

### Design Principle

The interface should not directly call GitHub APIs or agent tools. All analysis must go through the application service and orchestration layers.

---

## 6.4.2 Application Service Layer

The Application Service Layer manages the analysis workflow at the application level.

It acts as the boundary between the user interface and the multi-agent system.

### Responsibilities

* Receive repository-analysis requests.
* Validate user input before analysis begins.
* Create an analysis session identifier.
* Apply repository size, file count, and time limits.
* Start and monitor the multi-agent workflow.
* Return structured analysis results to the interface.
* Handle errors without exposing stack traces or credentials.
* Support partial result delivery where appropriate.

### Key Components

| Component                  | Responsibility                                                |
| -------------------------- | ------------------------------------------------------------- |
| Repository Input Validator | Validates GitHub URL and `owner/repository` format            |
| Analysis Session Manager   | Tracks request state, timing, status, and result availability |
| Analysis Controller        | Starts orchestration and returns the final report             |
| Error Translator           | Converts technical failures into plain-language user messages |
| Response Formatter         | Converts structured analysis results into UI-ready output     |

---

## 6.4.3 Multi-Agent Orchestration Layer

The Multi-Agent Orchestration Layer is the decision-making core of the system.

It contains the Coordinator Agent and specialized analysis agents. The Coordinator Agent manages workflow planning, delegation, result synthesis, conflict handling, and final recommendation prioritization.

### Coordinator Agent Responsibilities

The Coordinator Agent shall:

* Confirm that repository intake data is sufficient.
* Select relevant specialist agents based on repository type.
* Assign scoped tasks to specialist agents.
* Pass only required repository evidence to each agent.
* Collect structured findings.
* Detect duplicate or overlapping recommendations.
* Resolve conflicting recommendations.
* Rank recommendations by severity, impact, effort, and confidence.
* Generate the final report structure.
* Mark incomplete analysis areas when tools or agents fail.

### Why a Coordinator Agent Is Required

A single agent can produce general feedback, but it is more likely to be inconsistent, repetitive, and difficult to evaluate. The Coordinator Agent provides governance over specialist outputs and ensures the final report reads like one coherent professional assessment.

---

# 6.5 Specialist Agent Architecture

Each specialist agent has one primary responsibility and produces structured findings rather than unrestricted narrative output.

| Agent                      | Primary Responsibility                                       | Typical Evidence                                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------- |
| Repository Structure Agent | Understand repository layout and identify high-value files   | File tree, root files, manifests, directories                 |
| Documentation Agent        | Assess README and supporting documentation quality           | README, docs folder, setup files, deployment guidance         |
| Quality Agent              | Review maintainability and engineering-practice signals      | Tests, CI workflows, linting, formatting, project structure   |
| Security Agent             | Identify lightweight security hygiene risks                  | Configuration files, `.gitignore`, secret-like patterns       |
| Discoverability Agent      | Review GitHub presentation and community readiness           | Description, topics, license, templates, releases             |
| Portfolio Readiness Agent  | Assess project presentation for recruiters and collaborators | README, demos, screenshots, metrics, architecture explanation |

---

## 6.5.1 Repository Structure Agent

### Purpose

Build a concise repository inventory that other agents can use safely and efficiently.

### Responsibilities

* Retrieve high-level file tree.
* Identify source, test, documentation, configuration, and deployment folders.
* Detect common package and dependency files.
* Identify unsupported or large generated directories.
* Create a prioritized file-access plan.
* Infer repository category at a high level.

### Output

* Repository structure summary.
* Important files found.
* Important files missing.
* Candidate files for deeper inspection.
* Repository type signals.
* Confidence level.

---

## 6.5.2 Documentation Agent

### Purpose

Evaluate whether developers, users, contributors, and recruiters can understand and use the project.

### Responsibilities

* Review README structure and content.
* Check for installation, configuration, usage, testing, and deployment instructions.
* Check for architecture, limitations, troubleshooting, and contribution guidance.
* Identify missing documentation artifacts.
* Recommend documentation improvements.
* Generate draft documentation content when explicitly requested.

### Output

* Documentation coverage assessment.
* Missing documentation sections.
* Evidence references.
* Suggested improvements.
* Optional draft-content request options.

---

## 6.5.3 Quality Agent

### Purpose

Evaluate repository-level signals associated with maintainability and engineering maturity.

### Responsibilities

* Inspect project organization.
* Check for tests and test configuration.
* Check for linting, formatting, and type-checking configuration.
* Identify CI/CD workflows.
* Review dependency management signals.
* Detect environment-configuration practices.
* Identify missing engineering hygiene artifacts.

### Output

* Maintainability findings.
* Engineering-practice observations.
* Missing quality controls.
* Improvement recommendations.
* Confidence level.

---

## 6.5.4 Security Agent

### Purpose

Perform a lightweight repository security hygiene review while preventing exposure of sensitive content.

### Responsibilities

* Identify configuration and credential-related files.
* Scan approved files for secret-like patterns.
* Check whether `.env`, credential files, or sensitive configuration may be tracked.
* Review `.gitignore` for common secret and environment exclusions.
* Check for `SECURITY.md`.
* Redact sensitive-looking values before findings are returned.
* Classify findings by confidence and risk type.

### Output

* Redacted security findings.
* Risk classification.
* File path references.
* Safe remediation guidance.
* Security-review limitations.

### Important Boundary

The Security Agent does not claim to provide a vulnerability assessment, penetration test, or complete secret scan. It identifies repository-level hygiene concerns only.

---

## 6.5.5 Discoverability Agent

### Purpose

Assess how well the repository is positioned for discovery, trust, and community participation.

### Responsibilities

* Review repository description and topics.
* Check for license information.
* Check for contribution guidance.
* Check for issue and pull-request templates.
* Review release and changelog signals.
* Check for code of conduct and support guidance.
* Recommend GitHub presentation improvements.

### Output

* Discoverability assessment.
* Community-readiness findings.
* Missing GitHub ecosystem files.
* Prioritized recommendations.

---

## 6.5.6 Portfolio Readiness Agent

### Purpose

Assess whether the repository demonstrates the developer’s work effectively.

### Responsibilities

* Evaluate project purpose and real-world value explanation.
* Check for architecture diagrams, screenshots, demos, or video links.
* Check for measurable results, benchmarks, or evaluation metrics.
* Review reproducibility and onboarding clarity.
* Identify missing portfolio-oriented content.
* Recommend professional presentation improvements.

### Output

* Portfolio-readiness assessment.
* Presentation gaps.
* Suggested README sections.
* Evidence-based recommendations.

---

# 6.6 MCP and GitHub Integration Architecture

The system will use a GitHub MCP server or a controlled GitHub API adapter as the repository-access layer.

Agents should not make unrestricted network calls or execute arbitrary repository code. Instead, repository access should occur through approved tools with constrained inputs and outputs.

```text id="arch02"
Coordinator Agent
        │
        ▼
Controlled Tool Gateway
        │
        ├── Validate repository identifier
        ├── Enforce allowlisted operations
        ├── Enforce file-size and file-count limits
        ├── Apply rate-limit handling
        ├── Redact sensitive content
        └── Normalize tool responses
        │
        ▼
GitHub MCP Server / GitHub API
        │
        ├── Get repository metadata
        ├── Get repository tree
        ├── Get approved file contents
        ├── Get topics and license
        ├── Get releases and workflows
        └── Get issue-template metadata
```

### Supported GitHub Operations for MVP

The MCP integration should support only the operations required for analysis:

* Retrieve repository metadata.
* Retrieve repository file tree.
* Retrieve selected text file content.
* Retrieve README content.
* Retrieve topics and license information.
* Retrieve workflow metadata.
* Retrieve release information where available.
* Retrieve repository description and public visibility information.

### Explicitly Restricted Operations

The MCP integration must not permit agents to:

* Push commits.
* Create branches.
* Open pull requests.
* Merge pull requests.
* Delete repository files.
* Change repository settings.
* Add users or collaborators.
* Modify repository visibility.
* Access private repositories in the MVP.

---

# 6.7 Data Flow

The following data flow describes a standard analysis request.

```text id="arch03"
1. User submits repository URL or owner/repository
        ↓
2. Input Validator checks format
        ↓
3. GitHub Tool retrieves public repository metadata
        ↓
4. Repository Structure Agent retrieves file tree
        ↓
5. Tool Gateway selects safe, high-value files
        ↓
6. Coordinator Agent delegates evidence to specialist agents
        ↓
7. Specialist agents return structured findings
        ↓
8. Security Guardrail redacts sensitive values
        ↓
9. Coordinator Agent deduplicates and prioritizes findings
        ↓
10. Scoring Engine calculates heuristic category scores
        ↓
11. Report Generator creates final structured report
        ↓
12. UI displays findings, limitations, and optional draft content
```

---

# 6.8 Structured Data Contracts

All important agent inputs and outputs should use typed schemas.

This reduces ambiguity, improves evaluation, simplifies testing, and makes it easier to add agents later.

## Example Finding Schema

```json
{
  "finding_id": "DOC-001",
  "agent": "documentation_agent",
  "category": "documentation",
  "title": "Missing installation instructions",
  "priority": "high",
  "confidence": "high",
  "evidence": [
    {
      "type": "file",
      "path": "README.md",
      "summary": "README describes project purpose but does not include installation commands."
    }
  ],
  "why_it_matters": "New users cannot reliably set up the project.",
  "recommended_action": "Add prerequisites, installation commands, and verification steps.",
  "suggested_location": "README.md > Installation",
  "estimated_effort": "small",
  "requires_human_review": true
}
```

## Example Agent Result Schema

```json
{
  "agent_name": "quality_agent",
  "status": "completed",
  "summary": "The repository has clear source separation but lacks automated testing and CI validation.",
  "findings": [],
  "limitations": [],
  "confidence": "medium"
}
```

## Example Final Report Schema

```json
{
  "repository_summary": {},
  "technology_stack": [],
  "health_score": {},
  "agent_statuses": [],
  "key_findings": [],
  "recommendations": [],
  "generated_drafts": [],
  "limitations": [],
  "disclaimer": ""
}
```

---

# 6.9 Security and Guardrail Architecture

Security controls must operate across the system rather than being limited to the Security Agent.

## Guardrail Layers

| Layer                    | Guardrail                                                       |
| ------------------------ | --------------------------------------------------------------- |
| Input Layer              | Validate GitHub URL and repository identifier format            |
| Repository Access Layer  | Public repositories only; allowlisted API and MCP operations    |
| File Retrieval Layer     | Restrict file sizes, file types, and total retrieved content    |
| Content Processing Layer | Redact secret-like values before further processing             |
| Agent Layer              | Restrict agents to defined responsibilities and approved tools  |
| Output Layer             | Remove sensitive content, raw errors, and unsupported certainty |
| Logging Layer            | Avoid storing credentials or full sensitive file content        |

## Security Principles

* Least privilege.
* Safe defaults.
* No autonomous repository modification.
* Redaction before report generation.
* Clear distinction between suspicious patterns and confirmed exposure.
* Human review for all recommendations and generated drafts.

---

# 6.10 Scoring and Recommendation Architecture

The health score is a heuristic summary, not a universal measurement of software quality.

The system will calculate category-level scores from structured agent findings.

## Proposed Score Categories

| Category             | Example Inputs                                                                   |
| -------------------- | -------------------------------------------------------------------------------- |
| Documentation        | README quality, setup steps, usage, architecture, contribution guidance          |
| Maintainability      | Tests, CI, organization, configuration, dependency management                    |
| Security Hygiene     | Secret exposure indicators, `.gitignore`, environment practices, security policy |
| Discoverability      | Description, topics, license, community health files, releases                   |
| Portfolio Readiness  | Problem statement, demos, screenshots, outcomes, reproducibility                 |
| Developer Experience | Setup clarity, local development guidance, troubleshooting, onboarding           |

## Scoring Principles

* Scores must include a category-level explanation.
* Missing evidence should reduce confidence, not automatically imply poor quality.
* Critical security findings should be visible regardless of overall score.
* The system should avoid false precision, such as claiming a repository is exactly “82.4% professional.”
* Score calculations should be deterministic where possible.

---

# 6.11 Failure Handling Architecture

The system must remain transparent and useful when an external dependency, tool, or agent fails.

## Failure Categories

| Failure Type             | Expected Behavior                                                      |
| ------------------------ | ---------------------------------------------------------------------- |
| Invalid repository input | Stop early and provide format guidance                                 |
| Repository inaccessible  | Explain that the repository cannot be analyzed                         |
| Rate limit reached       | Return a controlled service message and preserve no misleading results |
| File retrieval failure   | Continue with available evidence and mark limitations                  |
| Specialist agent failure | Continue if non-critical; mark that category incomplete                |
| Coordinator failure      | Return available structured results only if safely assembled           |
| Secret detection issue   | Default to redaction and avoid displaying possible sensitive values    |

## Partial Analysis Rule

When one or more non-critical components fail, the final report must include:

* Completed areas.
* Incomplete areas.
* Reason for limitation.
* A statement that no conclusion should be drawn from missing analysis.

---

# 6.12 Deployment Architecture for the Capstone MVP

The initial deployment should remain lightweight and easy to demonstrate.

```text id="arch04"
User Browser
      │
      ▼
Streamlit Frontend
      │
      ▼
Python Application Backend
      │
      ├── Google ADK Agent Orchestration
      ├── Guardrails and Validation
      ├── GitHub MCP Client / API Adapter
      ├── Scoring and Reporting Services
      └── Structured Logging
      │
      ▼
GitHub MCP Server / GitHub Public API
```

## Recommended MVP Deployment Model

* **Frontend:** Streamlit for rapid, polished demonstration.
* **Backend:** Python application modules running with Google ADK.
* **Integration:** GitHub MCP server or GitHub API adapter.
* **Configuration:** Environment variables for credentials and runtime limits.
* **Storage:** Session-only or lightweight local storage for capstone demonstrations.
* **Logging:** Structured local logs with redaction.

This approach is appropriate for the competition because it demonstrates a real agent workflow without introducing unnecessary infrastructure complexity.

---

# 6.13 Future Architecture Extensions

The following capabilities are intentionally deferred but supported by the architecture:

* GitHub OAuth for authenticated access.
* Private repository analysis.
* GitHub App integration.
* Pull-request generation with approval workflows.
* Scheduled repository-health monitoring.
* Team dashboards and organization-wide reporting.
* Persistent report history.
* Integration with dependency scanners.
* Integration with CI/CD systems.
* Support for GitLab and Bitbucket.
* Multi-repository portfolio analysis.
* Repository benchmarking against quality standards.

---
