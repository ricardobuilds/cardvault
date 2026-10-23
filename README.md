# CardVault

CardVault is a personal collection-management application for tracking and organizing English-language One Piece Trading Card Game (TCG) cards.

The goal is to make it easier to keep track of which cards I own, distinguish between collectible versions of the same card, and manage quantities without repeatedly entering the same information.

## Project Status

**Current status:** Pre-Phase 1 — Repository & Planning (in progress).

The project foundation and development environment are complete. The README and development roadmap have been established, and repository planning is being finalized before beginning requirements analysis.

Collection-management functionality has not yet been implemented.

## Preliminary MVP Features

The following capabilities are being considered for the initial MVP and will be evaluated during requirements analysis:

- **Card search:** Find recorded cards by name or card number.
- **Collectible versions:** Distinguish versions of the same card, such as alternate artwork, promotional printings, and reprints.
- **Manual card entry:** Record card information and quantities when a collectible version is not already in the collection.
- **Quantity management:** Increase the quantity owned of an existing collectible version without duplicating its card information.
- **Collection browsing:** View recorded cards, their collectible versions, and quantities owned.

The initial version will focus on a personal English-language collection. Additional functionality will be considered as the project evolves.

## Current Technology Stack

CardVault currently uses the following technologies:

- **Backend:** Ruby on Rails (API-only)
- **Database:** PostgreSQL
- **Local development:** Docker and Docker Compose
- **Testing:** Rails' built-in testing framework

Rails runs directly on macOS during local development, while PostgreSQL runs in a Docker container.

Specific dependency versions are maintained in the project's configuration and lockfiles. Technologies and versions may evolve as the application's requirements change.

## Getting Started

See the [Development Setup Guide](docs/SETUP.md) for installation, configuration, and local development instructions.

## Documentation

- [Project Proposal](docs/PROJECT_PROPOSAL.md) — Project motivation, initial requirements, scope, and technology decisions.
- [Development Setup Guide](docs/SETUP.md) — Local environment setup and development commands.
- [Development Roadmap](docs/ROADMAP.md) — Development phases, guiding questions, planned activities, and completion criteria.

## AI-Assisted Development

CardVault is developed with the assistance of AI tools for technical research, planning, design discussions, documentation, and implementation support.

Architectural decisions, requirements, and generated code are reviewed and evaluated before being incorporated into the project.

## Future Development

Potential future enhancements include external card-data integration, card scanning, collection statistics, pricing information, and additional user-facing clients.

These are possibilities rather than requirements for the initial version.
