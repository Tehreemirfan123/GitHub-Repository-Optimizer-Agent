# Documentation Strategy

### 17.1 Purpose

This document defines the documentation plan for the GitHub Repository Optimizer Agent.

The documentation must make the project easy to understand, run, evaluate, extend, and submit for the Google × Kaggle AI Agents Capstone Project.

---

# 17.2 Documentation Goals

The project documentation should:

* Explain the problem clearly.
* Show why the project is useful.
* Describe the multi-agent architecture.
* Explain Google ADK usage.
* Explain MCP integration.
* Document security boundaries.
* Show evaluation evidence.
* Help users run the project locally.
* Help reviewers understand the engineering decisions.
* Present the project professionally on GitHub.

---

# 17.3 Documentation Structure

```text
docs/
├── product/
│   ├── problem_definition.md
│   ├── project_scope.md
│   ├── requirements.md
│   ├── user_personas.md
│   └── use_cases.md
│
├── architecture/
│   ├── system_architecture.md
│   ├── multi_agent_architecture.md
│   ├── adk_design.md
│   └── mcp_integration.md
│
├── engineering/
│   ├── agent_skills.md
│   ├── security_design.md
│   ├── evaluation_strategy.md
│   ├── technology_stack.md
│   ├── folder_structure.md
│   └── implementation_plan.md
│
├── adr/
│   ├── ADR-001-modular-monolith.md
│   ├── ADR-002-read-only-github-access.md
│   ├── ADR-003-streamlit-ui.md
│   └── ADR-004-session-only-storage.md
│
└── demo/
    ├── demo_script.md
    ├── demo_repositories.md
    └── screenshots/
```

---

# 17.4 Root README

The root `README.md` is the most important documentation file.

It should provide a complete project overview without forcing users to open every design document.

## README Sections

The README should include:

1. Project title
2. Short project summary
3. Problem statement
4. Key features
5. Demo screenshot or GIF
6. System architecture overview
7. Multi-agent design
8. Google ADK usage
9. MCP integration
10. Security and guardrails
11. Evaluation summary
12. Technology stack
13. Installation
14. Environment variables
15. Usage instructions
16. Example output
17. Limitations
18. Roadmap
19. Contributing
20. License

---

# 17.5 Documentation Standards

All documentation should follow these standards:

* Use clear headings.
* Keep explanations concise.
* Avoid exaggerated marketing language.
* Explain why major design decisions were made.
* Use diagrams where they improve understanding.
* Keep terminology consistent.
* Avoid unsupported claims.
* Clearly distinguish implemented features from future roadmap items.
* Keep setup instructions tested and accurate.

---

# 17.6 Required Diagrams

The repository should include the following diagrams:

| Diagram                      | Purpose                                                          |
| ---------------------------- | ---------------------------------------------------------------- |
| System architecture diagram  | Shows UI, services, agents, tools, MCP, and GitHub               |
| Multi-agent workflow diagram | Shows Coordinator Agent and specialist agents                    |
| MCP integration diagram      | Shows read-only GitHub access boundary                           |
| Security boundary diagram    | Shows validation, redaction, and output controls                 |
| Evaluation flow diagram      | Shows fixtures, expected findings, agent evaluation, and reports |

These diagrams may be created using Mermaid inside Markdown for simplicity.

---

# 17.7 Architecture Decision Records

Architecture Decision Records should document important choices.

## ADR Format

Each ADR should include:

* Title
* Status
* Context
* Decision
* Alternatives considered
* Consequences

## Initial ADRs

| ADR     | Decision                                     |
| ------- | -------------------------------------------- |
| ADR-001 | Use modular monolith architecture            |
| ADR-002 | Use read-only public GitHub access for MVP   |
| ADR-003 | Use Streamlit for the MVP interface          |
| ADR-004 | Use session-only storage                     |
| ADR-005 | Use deterministic guardrails with LLM agents |
| ADR-006 | Use fixture-based evaluation                 |

---

# 17.8 User Documentation

User documentation should help someone run and use the project.

It should include:

* Prerequisites
* Installation commands
* Environment setup
* Running the Streamlit app
* Submitting a repository
* Reading the report
* Understanding health scores
* Generating draft content
* Understanding limitations

---

# 17.9 Developer Documentation

Developer documentation should help future contributors understand the codebase.

It should include:

* Folder structure
* Agent responsibilities
* Skill responsibilities
* Tool gateway design
* Schema models
* Configuration system
* How to add a new agent
* How to add a new skill
* How to add an evaluation fixture
* How to run checks locally

---

# 17.10 Security Documentation

Security documentation should explain:

* The project is analysis-only.
* The MVP supports public repositories only.
* The system does not push commits or modify repositories.
* Repository content is treated as untrusted data.
* Secret-like values are redacted.
* Prompt injection is handled through guardrails.
* The security review is not a complete vulnerability audit.

This content should appear in both `SECURITY.md` and the root README.

---

# 17.11 Evaluation Documentation

Evaluation documentation should show evidence that the system works.

It should include:

* Evaluation fixture list
* Expected findings approach
* Metrics used
* Safety tests
* Prompt-injection tests
* Secret-redaction tests
* Sample evaluation report
* Known limitations

The goal is to prove that the system was evaluated, not only demonstrated.

---

# 17.12 Demo Documentation

The `docs/demo/` folder should include:

* Demo script
* Demo repositories or fixtures
* Expected demo flow
* Screenshots
* Fallback plan
* Key talking points
* Kaggle submission notes

The demo should highlight:

* Multi-agent coordination
* Google ADK usage
* MCP integration
* Tool calling
* Security guardrails
* Evaluation evidence
* Practical usefulness

---
