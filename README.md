# CardVault

CardVault is a personal collection-management application for tracking and organizing English-language One Piece Trading Card Game (TCG) cards.

The goal is to make it easier to keep track of which cards I own, distinguish between collectible versions of the same card, and manage quantities without repeatedly entering the same information.

## Project Status

**Current status:** Phase 1 — Requirements & Use Case Design completed.

The project foundation, development environment, repository planning, and MVP requirements analysis are complete.

The MVP's functional requirements, business rules, use cases, and acceptance criteria have been defined and approved in [USE_CASES.md](docs/USE_CASES.md).

**Next phase:** Phase 2 — Domain Modeling & Database Design.

The next stage focuses on designing the domain model, database schema, relationships, and data-integrity constraints needed to support the approved MVP requirements.

Collection-management functionality has not yet been implemented.

## MVP Features

The following capabilities have been approved for the initial MVP:

- **Card search:** Search recorded cards by name.
- **Collectible versions:** Distinguish supported variants of the same card using the approved identification rules.
- **Manual card entry:** Record card information and quantities when a collectible version is not already in the collection.
- **Quantity management:** Increase or decrease the quantity owned of an existing collectible version without duplicating its card information.
- **Collection browsing:** View recorded cards, their collectible versions, and quantities owned.
- **Individual card retrieval:** Retrieve the information for a specific collectible record.
- **Collection removal:** Delete collectible records when they are no longer owned or needed.

The initial MVP focuses on a personal English-language collection managed through a local API using Postman. A graphical user interface, authentication, multi-user support, and external card-data integration are outside the initial scope.

Detailed requirements, supported card information, validation rules, API behavior, and acceptance criteria are documented in [USE_CASES.md](docs/USE_CASES.md).

## Current Technology Stack

CardVault currently uses or plans to use the following technologies:

- **Backend:** Ruby on Rails (API-only)
- **Database:** PostgreSQL
- **Local development:** Docker and Docker Compose
- **API client:** Postman
- **Testing:** RSpec (planned for automated model and request testing)

Rails runs directly on macOS during local development, while PostgreSQL runs in a Docker container.

Specific dependency versions are maintained in the project's configuration and lockfiles. Technologies and versions may evolve as the application's requirements change.

## Getting Started

See the [Development Setup Guide](docs/SETUP.md) for installation, configuration, and local development instructions.

## Documentation

- [Project Proposal](docs/PROJECT_PROPOSAL.md) — Project motivation, vision, scope, and technology decisions.
- [MVP Requirements & Use Cases](docs/USE_CASES.md) — Approved business rules, collection-management workflows, API behavior, and acceptance criteria.
- [Development Setup Guide](docs/SETUP.md) — Local environment setup and development commands.
- [Development Roadmap](docs/ROADMAP.md) — Development phases, guiding questions, activities, completion criteria, and project progress.

## AI-Assisted Development

CardVault is developed with the assistance of AI tools for technical research, planning, design discussions, documentation, and implementation support.

Architectural decisions, requirements, and generated code are reviewed and evaluated before being incorporated into the project.

## Future Development

Potential future enhancements include external card-data integration, card-number search, advanced filtering, additional collectible variants, card scanning, collection statistics, pricing information, and additional user-facing clients.

These are possibilities rather than requirements for the initial version.
