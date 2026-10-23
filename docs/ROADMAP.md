# CardVault Development Roadmap

## Overview

This document outlines the development plan for CardVault, a personal collection-management application for English-language One Piece Trading Card Game (TCG) cards.

The roadmap covers the work required to develop the minimum viable product (MVP), from project setup and requirements analysis through design, implementation, technical verification, and validation.

Each phase includes a guiding question, objective, activities, and completion criteria. The roadmap may evolve as requirements are clarified and technical decisions are made.

## Development Approach

CardVault follows a requirements-driven, iterative development process emphasizing clear requirements, deliberate technical decisions, and incremental implementation.

The following principles guide development:

1. **Requirements before design:** Define expected behavior, use cases, and acceptance criteria before making detailed design decisions.
2. **Design based on requirements:** Evaluate domain models, database structures, and API contracts against application needs.
3. **Incremental implementation:** Build and validate functionality in manageable increments.
4. **Data integrity and reliability:** Use appropriate validation, database constraints, and automated tests to maintain consistent behavior.
5. **Maintainability:** Favor understandable solutions and avoid unnecessary complexity.
6. **Documentation:** Keep requirements, technical decisions, and implementation documentation current.
7. **Practical validation:** Evaluate the application using real collection-management scenarios.

### Iterative Development

Development phases establish the primary sequence of work but are not strict boundaries. Requirements, domain models, database designs, and API contracts may be revisited as implementation and testing reveal new information.

Implementation will be organized into manageable iterations that include design, development, automated testing, and verification against acceptance criteria. Testing will occur throughout development rather than being deferred to a dedicated phase.

Iteration scope and sequencing will depend on requirements and technical dependencies. Significant changes will be evaluated against the agreed MVP scope and documented when appropriate.

## Development Phases

| Phase | Focus | Status |
|---|---|---|
| Phase 0 | Project Foundation & Development Environment | Completed |
| Pre-Phase 1 | Repository & Planning | Completed |
| Phase 1 | Requirements & Use Case Design | In progress |
| Phase 2 | Domain Modeling & Database Design | Not started |
| Phase 3 | API Design & Implementation | Not started |
| Phase 4 | Testing, Refinement & Documentation | Not started |
| Phase 5 | MVP Validation | Not started |

---

## Phase 0 — Project Foundation & Development Environment

**Status:** Completed

*Guiding question: What foundation do we need to begin developing CardVault?*

### Objective

Establish the project's direction, select the initial technology stack, configure the development environment, and document the foundation for application development.

### Completed Activities

- Defined the project's purpose, initial scope, and preliminary MVP capabilities.
- Created `docs/PROJECT_PROPOSAL.md` to document the project direction, preliminary requirements, and technology decisions.
- Evaluated and selected Ruby on Rails, PostgreSQL, and Docker Compose.
- Configured Ruby, Rails, Bundler, Git, GitHub CLI, Docker, and supporting development tools.
- Initialized and verified the Rails API-only application.
- Configured PostgreSQL through Docker Compose for development and test environments.
- Reviewed generated Rails configuration, including caching, background jobs, real-time communication, containerization, and deployment settings.
- Established the GitHub repository and version-control workflow.
- Created `docs/SETUP.md` with development prerequisites and local setup instructions.

### Completion Criteria

- [x] Project direction and technology choices documented.
- [x] Rails API-only application initialized and running.
- [x] PostgreSQL accessible to Rails in development and test environments.
- [x] Local development setup documented in `docs/SETUP.md`.
- [x] Foundational configuration committed to GitHub.

---

## Pre-Phase 1 — Repository & Planning

**Status:** Completed

*Guiding question: How will we organize and plan the development of CardVault?*

### Objective

Organize the repository, communicate the project's purpose and current state, and establish the development roadmap before detailed requirements analysis.

### Completed Activities

- Update the root `README.md` with a project overview, preliminary MVP scope, technology stack, and documentation links.
- Define development phases, objectives, activities, and completion criteria.
- Document the development plan in `docs/ROADMAP.md`.
- Review the GitHub repository description and documentation links for accuracy.

### Completion Criteria

- [x] Root `README.md` updated and merged into `main`.
- [x] Development phases and sequence agreed upon.
- [x] `docs/ROADMAP.md` completed, reviewed, and committed.
- [x] GitHub repository information and documentation links reviewed.

---

## Phase 1 — Requirements & Use Case Design

**Status:** In progress

*Guiding question: What must CardVault do?*

### Objective

Define and approve the MVP's functional requirements, expected behavior, and operational expectations before detailed database and API design.

### Planned Activities

- Review and refine the preliminary requirements in `docs/PROJECT_PROPOSAL.md`.
- Identify application actors and their interactions with the system.
- Evaluate proposed collection-management workflows, including card identification, collection recording, ownership management, and collection navigation.
- Define use cases and document their preconditions, main flows, alternative flows, and exceptions.
- Establish acceptance criteria for the expected behavior of each approved workflow.
- Identify relevant edge cases, invalid inputs, and ambiguous data scenarios.
- Confirm the MVP scope and identify capabilities to defer.
- Determine whether a user-facing client is necessary for the MVP.
- Establish runtime, deployment, and operational expectations.

### Completion Criteria

- [ ] `docs/USE_CASES.md` created and reviewed.
- [ ] MVP use cases and acceptance criteria documented.
- [ ] Important edge cases and error scenarios addressed.
- [ ] MVP scope and user-facing client requirements confirmed.
- [ ] Runtime and operational expectations established.
- [ ] Requirements approved before detailed design begins.

---

## Phase 2 — Domain Modeling & Database Design

**Status:** Not started

*Guiding question: How should CardVault represent and persist its data?*

### Objective

Design and implement a domain model and database schema that support the approved MVP requirements while maintaining data integrity and consistency.

### Planned Activities

- Review the approved requirements and use cases.
- Identify domain entities, attributes, relationships, and business rules.
- Evaluate approaches to representing and organizing application data.
- Design the relational schema, including tables, columns, data types, and keys.
- Determine appropriate database constraints, indexes, and transaction requirements.
- Create an entity-relationship diagram and validate the design against representative application scenarios.
- Document the database design and relevant decisions in `docs/DATABASE_DESIGN.md`.
- Implement Rails migrations and Active Record models.
- Develop automated tests for model behavior and database integrity.

### Completion Criteria

- [ ] Domain entities, relationships, and business rules documented.
- [ ] Database design validated against MVP requirements.
- [ ] `docs/DATABASE_DESIGN.md` completed, including an entity-relationship diagram.
- [ ] Database constraints and integrity requirements implemented.
- [ ] Rails migrations and Active Record models implemented.
- [ ] Automated tests verify important model behavior and database constraints.

---

## Phase 3 — API Design & Implementation

**Status:** Not started

*Guiding question: How should CardVault expose and implement the approved functionality?*

### Objective

Design, implement, and incrementally test the backend API required to support the MVP.

### Planned Activities

- Review the approved requirements, use cases, and domain model.
- Identify API resources and operations needed to support the required functionality.
- Define API contracts, including endpoints, HTTP methods, request and response formats, and error handling.
- Organize implementation into manageable iterations based on dependencies and complexity.
- Implement approved API functionality with appropriate validation and consistent error handling.
- Develop automated tests alongside each implementation iteration.
- Verify implemented behavior against use cases and acceptance criteria.
- Refine API design decisions as implementation progresses.
- Implement required runtime configuration and operational capabilities.

### Completion Criteria

- [ ] API contracts documented and reviewed.
- [ ] Approved MVP backend functionality implemented.
- [ ] Request validation and error handling behave consistently.
- [ ] Automated tests cover implemented functionality and important edge cases.
- [ ] Required runtime configuration and operational capabilities implemented.
- [ ] API behavior satisfies approved use cases and acceptance criteria.

---

## Phase 4 — Testing, Refinement & Documentation

**Status:** Not started

*Guiding question: Is the integrated implementation reliable and maintainable?*

### Objective

Verify the technical quality of the integrated MVP implementation, resolve outstanding defects, and ensure the system is adequately documented.

### Planned Activities

- Review automated test coverage against the MVP requirements.
- Verify integration between implemented components and test system-level behavior.
- Test important data-integrity, concurrency, and error-handling scenarios.
- Review API consistency, validation behavior, and error responses.
- Identify and resolve technical defects.
- Evaluate relevant database queries and application performance.
- Refactor code where improvements are justified.
- Verify runtime configuration and operational behavior against agreed expectations.
- Document implemented API endpoints and update relevant technical documentation.

### Completion Criteria

- [ ] Critical MVP workflows have appropriate automated test coverage.
- [ ] Integration and reliability requirements verified.
- [ ] Known MVP-blocking technical defects resolved.
- [ ] API and development documentation reflect the implemented system.
- [ ] Runtime and operational expectations verified.
- [ ] Technical verification completed against agreed acceptance criteria.

---

## Phase 5 — MVP Validation

**Status:** Not started

*Guiding question: Does the completed application meet its intended purpose?*

### Objective

Validate that CardVault fulfills its intended collection-management purpose through practical evaluation of the completed MVP.

### Planned Activities

- Define representative validation scenarios based on approved use cases.
- Exercise the application using real English-language One Piece TCG collection data.
- Evaluate whether implemented workflows meet collection-management needs.
- Assess the practicality and usability of the agreed MVP interface.
- Identify and resolve issues that prevent effective use.
- Review validation results against the MVP's acceptance criteria.
- Document the MVP completion assessment and remaining limitations.
- Prioritize improvements for subsequent development.

### Completion Criteria

- [ ] Approved workflows validated using representative real collection data.
- [ ] Application supports the intended collection-management needs.
- [ ] Agreed MVP interface supports practical use.
- [ ] MVP-blocking functional and usability issues resolved.
- [ ] Validation results and remaining limitations documented.
- [ ] MVP completion confirmed against approved requirements.
- [ ] Post-MVP improvements identified and prioritized.

---

## Potential Future Enhancements

The following capabilities may be evaluated after MVP validation:

- Integration with external card catalogs
- Card scanning and image recognition
- Collection statistics and analytics
- Card pricing and collection valuation
- Wishlist and deck-building functionality
- Trading and collection sharing
- Authentication and multi-user support
- Additional user-facing clients
- Application deployment, if deferred beyond the MVP

These are potential enhancements, not commitments for the initial MVP. Their priority will depend on future requirements and validation results.
