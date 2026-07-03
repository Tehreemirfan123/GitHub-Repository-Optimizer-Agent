# User Personas

### 4.1 Purpose

This document defines the primary users of the GitHub Repository Optimizer Agent.

The personas are based on common repository-management needs across students, job seekers, freelancers, open-source contributors, and small engineering teams. They help ensure that the product remains focused on practical user problems rather than only technical capabilities.

The initial capstone version will prioritize individual developers and small teams working with public GitHub repositories.

---

# 4.2 Persona 1 — Final-Year Computer Science Student

## Profile

**Name:** Ayesha Khan
**Role:** Final-year Computer Science student
**Experience Level:** Beginner to intermediate developer
**Typical Projects:** Machine learning projects, web applications, data science notebooks, university assignments, hackathon projects

Ayesha has built several technically strong projects but struggles to present them professionally on GitHub. She wants her repositories to strengthen her internship and job applications.

## Goals

* Make her GitHub repositories understandable to recruiters.
* Improve README files and setup instructions.
* Present projects professionally in her portfolio.
* Add screenshots, demos, architecture diagrams, and results.
* Avoid appearing inexperienced because of missing documentation.
* Learn what professional repositories usually contain.

## Pain Points

* She does not know what recruiters expect in a GitHub repository.
* Her projects often work locally but are difficult for others to run.
* Her README files are short, incomplete, or copied from templates.
* She is unsure whether she needs files such as `LICENSE`, `CONTRIBUTING.md`, or `.env.example`.
* She does not know how to explain machine learning results, metrics, or architecture clearly.
* She may accidentally commit API keys, model files, datasets, or environment files.

## Primary Jobs to Be Done

* “Help me make this project look professional before I share it on LinkedIn or apply for a job.”
* “Tell me what is missing from my repository.”
* “Help me write a README that another developer can actually use.”
* “Show me how to present my AI project clearly.”

## Most Valuable System Capabilities

* Portfolio readiness assessment.
* README and documentation quality review.
* Missing-file detection.
* Technology stack explanation.
* Suggested README sections.
* Security hygiene warnings.
* Prioritized improvement plan.
* Demo and screenshot recommendations.

## Success Criteria

Ayesha considers the product successful when she can:

* Understand what needs to improve without reading complex technical explanations.
* Update her repository using a clear checklist.
* Make her project easier for recruiters to evaluate.
* Feel confident sharing the repository publicly.

---

# 4.3 Persona 2 — Freelance Developer

## Profile

**Name:** Ahmed Raza
**Role:** Freelance full-stack developer
**Experience Level:** Intermediate developer
**Typical Projects:** Client dashboards, e-commerce applications, APIs, automation tools, small SaaS products

Ahmed uses GitHub to show work samples to potential clients. He needs repositories that demonstrate reliability, maintainability, and engineering quality.

## Goals

* Build client confidence through professional repositories.
* Show that projects are organized, maintainable, and secure.
* Improve onboarding documentation for collaborators.
* Ensure repositories are ready for code review or handover.
* Reduce the time spent manually checking repository quality.

## Pain Points

* Client projects may have weak documentation because delivery deadlines are tight.
* Setup instructions are often incomplete.
* Sensitive configuration files may be committed accidentally.
* Repositories may not include tests, CI workflows, or deployment guidance.
* He lacks a repeatable process for preparing a project before client handover.
* He wants practical recommendations rather than generic advice.

## Primary Jobs to Be Done

* “Review this repository before I show it to a client.”
* “Help me prepare this project for handover.”
* “Find risks that make the project look unprofessional.”
* “Tell me what documentation a new developer would need.”

## Most Valuable System Capabilities

* Maintainability assessment.
* Security review and secret redaction.
* Configuration and `.env.example` recommendations.
* CI/CD and testing checks.
* Handover documentation suggestions.
* Prioritized recommendations based on client-facing impact.

## Success Criteria

Ahmed considers the product successful when it helps him:

* Identify missing engineering standards quickly.
* Reduce avoidable handover problems.
* Improve client trust.
* Create a reusable quality-review process for future projects.

---

# 4.4 Persona 3 — Open-Source Maintainer

## Profile

**Name:** Sara Malik
**Role:** Open-source maintainer
**Experience Level:** Intermediate to advanced developer
**Typical Projects:** Developer tools, Python packages, JavaScript libraries, community projects, open-source APIs

Sara manages a public repository that receives occasional interest from contributors. She wants to make the project easier to understand, install, and contribute to.

## Goals

* Improve contributor onboarding.
* Make the repository easier to discover and trust.
* Reduce repetitive setup questions.
* Add missing community health files.
* Improve issue quality and pull-request consistency.
* Keep the project approachable for new contributors.

## Pain Points

* New contributors do not know how to set up the project.
* Issues may lack useful information.
* Pull requests may not follow a standard format.
* The repository may lack `CONTRIBUTING.md`, issue templates, or a code of conduct.
* Documentation may become outdated as the project evolves.
* GitHub topics and project description may be incomplete.

## Primary Jobs to Be Done

* “Help me make this repository easier for contributors to join.”
* “Identify what community and documentation files are missing.”
* “Show me how to improve GitHub discoverability.”
* “Help me make the project more trustworthy for new users.”

## Most Valuable System Capabilities

* Discoverability assessment.
* Community health file detection.
* Contribution-guidance review.
* Issue and pull-request template generation.
* Documentation gap analysis.
* Release and changelog recommendations.
* GitHub topic and repository-description suggestions.

## Success Criteria

Sara considers the product successful when it helps her:

* Improve onboarding for new contributors.
* Reduce repetitive project questions.
* Strengthen the repository’s open-source professionalism.
* Identify practical actions that improve adoption.

---

# 4.5 Persona 4 — Startup Engineering Lead

## Profile

**Name:** Bilal Siddiqui
**Role:** Engineering lead at a small startup
**Experience Level:** Advanced developer or technical manager
**Typical Projects:** Internal tools, prototypes, SaaS applications, APIs, data platforms, AI products

Bilal manages a small team where speed is important. The team frequently develops prototypes that later become production systems. He needs a lightweight way to assess whether a repository is ready for onboarding, collaboration, or external sharing.

## Goals

* Standardize repository quality across projects.
* Identify documentation and security gaps early.
* Improve developer onboarding.
* Reduce knowledge concentrated in one developer.
* Prepare repositories for investors, partners, or new hires.
* Create repeatable engineering standards.

## Pain Points

* Prototype repositories are often inconsistent.
* Documentation is incomplete because the team moves quickly.
* Configuration and deployment knowledge may exist only in team members’ memory.
* New developers take too long to understand projects.
* The team lacks time for formal repository audits.
* Security hygiene may be inconsistent across experiments.

## Primary Jobs to Be Done

* “Assess whether this repository is ready for another engineer to work on.”
* “Find documentation, configuration, and security risks before the team grows.”
* “Give me a prioritized backlog of repository improvements.”
* “Help us apply consistent standards across projects.”

## Most Valuable System Capabilities

* Repository health score.
* Structured engineering-quality report.
* Security and configuration review.
* Documentation and onboarding assessment.
* CI/CD and test coverage indicators.
* Prioritized recommendations with effort estimates.
* Exportable findings for planning work.

## Success Criteria

Bilal considers the product successful when it helps the team:

* Quickly identify project readiness gaps.
* Convert findings into an improvement backlog.
* Reduce onboarding time.
* Improve consistency without slowing development.

---

# 4.6 Persona 5 — AI and Data Science Project Builder

## Profile

**Name:** Hamza Iqbal
**Role:** Machine learning engineer or data science learner
**Experience Level:** Beginner to intermediate
**Typical Projects:** Jupyter notebooks, computer vision models, NLP applications, Streamlit dashboards, Kaggle projects, data pipelines

Hamza builds AI projects that may perform well technically but are hard for others to reproduce because datasets, model files, environments, and evaluation steps are not clearly documented.

## Goals

* Make AI projects reproducible.
* Clearly explain datasets, models, training, evaluation, and deployment.
* Present metrics and results professionally.
* Avoid committing large model files or sensitive credentials.
* Turn Kaggle or notebook work into a portfolio-ready GitHub project.

## Pain Points

* Notebook-based repositories are often difficult to navigate.
* Dataset access and preprocessing instructions are missing.
* Model checkpoints may be too large or improperly tracked.
* Results and evaluation metrics are not explained clearly.
* Environment setup may be inconsistent.
* README files do not explain model limitations or responsible-use considerations.

## Primary Jobs to Be Done

* “Help me turn this machine learning project into a professional GitHub repository.”
* “Show me what is missing for reproducibility.”
* “Help me document training, evaluation, and model results.”
* “Identify files that should not be committed.”

## Most Valuable System Capabilities

* ML-project structure detection.
* Dataset and model reproducibility checks.
* README recommendations for training and evaluation.
* Model-card and experiment-documentation suggestions.
* Large-file and secret hygiene warnings.
* Portfolio-readiness recommendations.
* Demo and benchmark presentation guidance.

## Success Criteria

Hamza considers the product successful when it helps him:

* Make an AI project easier to reproduce.
* Explain model results clearly.
* Improve project credibility for recruiters, Kaggle peers, and collaborators.
* Convert experimental work into a polished software repository.

---

# 4.7 Primary Persona Priority

The capstone MVP will prioritize the following personas:

| Priority  | Persona                             | Reason                                                          |
| --------- | ----------------------------------- | --------------------------------------------------------------- |
| Primary   | Final-Year Computer Science Student | Strong portfolio need and highly relatable competition use case |
| Primary   | AI and Data Science Project Builder | Closely aligned with Kaggle and AI-agent course audience        |
| Secondary | Freelance Developer                 | Strong practical need for professional repository quality       |
| Secondary | Open-Source Maintainer              | Demonstrates GitHub ecosystem relevance                         |
| Secondary | Startup Engineering Lead            | Supports production-quality positioning and future scalability  |

---

# 4.8 Shared User Needs

Despite their differences, all target users need the system to provide:

* Clear repository feedback.
* Evidence-based findings.
* Practical recommendations.
* Prioritized actions.
* Professional documentation guidance.
* Security hygiene checks.
* Easy-to-understand reports.
* Suggestions that match the project’s technology stack.
* Recommendations that can be reviewed before implementation.

---

# 4.9 Persona-Driven Product Principles

The following principles will guide future product decisions:

1. **Actionable over generic**
   Every finding should tell the user what to improve and where to improve it.

2. **Evidence before advice**
   Recommendations should be grounded in repository files, metadata, or observable gaps.

3. **Professional without being overwhelming**
   The product should provide strong engineering guidance without requiring advanced expertise.

4. **Safe by default**
   The product must not expose secrets, modify repositories without permission, or overstate security findings.

5. **Adapt recommendations to project context**
   A machine learning repository should receive different advice than a React application or Python package.

6. **Support growth from student project to professional repository**
   The system should help users improve incrementally rather than requiring enterprise-level maturity immediately.

---
