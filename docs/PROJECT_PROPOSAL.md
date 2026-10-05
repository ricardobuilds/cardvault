# CardVault — Initial Project Proposal

> **CardVault is a personal collection-management application for tracking and organizing my English-language One Piece Trading Card Game collection.**

## 1. Project Background and Motivation

While attending One Piece Fest in Los Angeles, I participated in a learn-to-play One Piece TCG session. I enjoyed the experience and soon began collecting One Piece cards myself. As my collection grew, I quickly found that keeping track of the cards I owned was more difficult than I expected.

CardVault is intended to solve that problem for my own collection first. Rather than beginning as a product designed to meet the needs of every One Piece TCG collector, the initial version will focus on collection-management capabilities that I personally find useful.

If CardVault proves useful for managing my collection, the project can evolve based on what I learn from actually using it.

## 2. Project Vision

The initial goal of CardVault is to give me a convenient way to record, organize, and search my card collection.

Card information will initially be entered manually. CardVault will not depend on having access to a complete catalog of One Piece TCG cards or an external source of card information in order to be useful.

The project should remain flexible enough to evolve as I use CardVault, discover limitations, and better understand which features would make it more useful.

## 3. First Useful Version

For CardVault to be useful for managing my collection, I need to be able to record all of the cards I own while avoiding unnecessary duplicate data entry.

When adding a card, I should first be able to determine whether the same collectible version has already been recorded in CardVault.

If the same collectible version already exists, CardVault should show the information for that card and how many copies I currently own. I should then be able to increase the quantity without entering all of the card information again.

If that collectible version has not already been recorded, I should be able to enter the information needed to identify and describe it and record the quantity I own.

I should also be able to view my collection and see the cards, collectible versions, and quantities I have recorded.

This defines the minimum workflow that would make CardVault genuinely useful to me. Additional capabilities can be added after this workflow is working.

## 4. Initial Card Information

The official One Piece Card Game rules identify several card categories, including Leader, Character, Event, Stage, and DON!! cards. The information displayed on a card varies depending on its category.

CardVault should record the applicable official information for each card. Depending on the card category, this information includes:

### Identity

- Card Name
- Card Number

### Classification and Metadata

- Card Category
- Rarity
- Block Symbol

### Gameplay Information

- Color
- Type/Traits
- Attribute
- Cost
- Power
- Counter
- Life
- Effects
- Trigger Effect

Not every field applies to every card category. For example, Leader and Character cards contain different combinations of information, while Event and Stage cards have their own applicable fields.

Cards can also have more than one color.

The Card Number already contains its product/set code, such as `OP01` in `OP01-025`, so the product/set code does not need to be entered separately.

### Collectible Versions

Card Number alone is not necessarily sufficient to distinguish the particular collectible version of a card.

For collection purposes, CardVault needs to distinguish English-language versions of the same underlying card when those differences matter to collecting. Relevant distinctions can include Alternate Art, Manga Alternate Art, Promo, Pre-Release/Super Pre-Release, SP/reprints, errata versions, serial-numbered versions, and other meaningful printing or artwork distinctions.

These distinctions are based in part on the collector-oriented information described by PSA in its One Piece collecting guide.

The exact database representation of these characteristics has **not** been decided. They represent product/domain information at this stage, not proposed database columns.

### Collection Information

In addition to information describing the card itself, CardVault needs to record information about my collection.

For the initial version, the primary collection-specific information is:

- **Quantity owned**

Multiple identical copies of the same collectible version will be represented by a quantity rather than requiring a separate collection entry for every physical copy.

Individual physical-copy characteristics such as condition, grading, or signatures are not required for the initial version.

## 5. Search and Collection Browsing

CardVault should allow me to search my collection by:

- **Card Name**
- **Card Number**

Search results should return all matching collectible versions that have been recorded in CardVault, together with their relevant card information and the quantity I own of each.

A Card Name search should not assume that the name identifies a single collection entry. If multiple collectible versions of that card have been recorded, I should be able to see each version and determine whether the particular version I am holding is already in my collection.

If it has already been recorded, I can increase its quantity rather than entering all of its information again.

### Filtering

The information CardVault records about cards and collectible versions may also be useful for browsing and filtering the collection.

However, not every piece of stored card information needs to become an initial filter.

Rather than defining a separate filtering taxonomy before using the application, initial filtering capabilities should be selected from the information CardVault already records based on what proves useful when managing the collection.

This keeps filtering aligned with the actual card and collectible-version information rather than introducing additional classification solely for search purposes.

The implementation of search and filtering will be determined later during API and data-model design.

## 6. Initial Scope Decisions

### English-Language Collection

CardVault will initially focus on the English-language One Piece TCG cards I collect. Supporting other language printings is outside the initial scope.

### Authentication

Authentication will be deferred while CardVault is a local application intended only for my personal use. It can be introduced if broader access creates a need for it.

### Additional Users

Multi-user support is intentionally deferred. CardVault should first become useful for managing my own collection before support for other collectors is considered.

## 7. Future Possibilities

Future versions of CardVault may obtain card information from an external data source rather than relying entirely on manual entry.

This could reduce repetitive data entry and potentially give CardVault knowledge of cards that I do not already own. Access to a broader card catalog could also make capabilities such as identifying missing cards from a set possible.

Other possible future capabilities include card scanning or image recognition, pricing information, collection statistics, wishlists, deck building, trading-related features, individual-card condition or grading information, support for additional users, and additional user-facing clients.

These are possibilities rather than commitments. They are not requirements for the initial version.

## 8. Initial Technology Direction

The following technologies represent the current starting direction. They provide a foundation for beginning development but can be revisited if the project's needs change.

### Ruby on Rails

Ruby on Rails was selected early as the backend framework for CardVault.

Rails provides a mature framework for building web applications and APIs and is well suited to the type of backend CardVault is expected to require. CardVault will need to accept and validate card information, persist and retrieve structured data, query a relational database, implement collection-management behavior, and expose that functionality through an HTTP API. Rails provides established conventions and integrated tooling for these responsibilities.

In particular, **Active Record** provides Rails' object-relational mapping layer for working with PostgreSQL, including models, relationships, validations, queries, and database migrations. Rails also provides routing and controllers for defining API behavior, along with built-in support for concerns such as request handling, parameter processing, serialization, testing, and environment-specific configuration.

Rails' convention-over-configuration approach also allows the project to spend less time assembling basic application infrastructure and more time designing CardVault's domain model and backend behavior. As the application grows, Rails provides an established structure for organizing those responsibilities while still allowing individual components to evolve when the requirements become clearer.

Rails also aligns with the development goals of CardVault. The project provides an opportunity to deepen practical experience with Rails and PostgreSQL by designing a backend application from its initial requirements through implementation, testing, and eventually deployment.

A broader framework comparison was not necessary for the initial technology decision because Rails was selected early as both a suitable framework for CardVault's requirements and one of the technologies the project is intended to exercise.

### API-Only Rails

CardVault will initially use Rails in API-only mode.

The initial focus of the project is the backend: modeling the collection domain, managing persistent data, implementing application behavior, and exposing that functionality through an HTTP API. API-only Rails supports that focus by configuring the application around API responsibilities rather than the server-rendered views and browser-oriented features provided by a traditional full-stack Rails application.

Separating the backend behind an API also means the eventual user-facing client does not need to be chosen now. A web, desktop, mobile, or other client could interact with CardVault through the same backend API without requiring the backend to be designed around a particular user-interface technology.

This separation introduces a tradeoff: CardVault will eventually need a separate client to provide a graphical interface. That tradeoff is acceptable for the initial project because developing and understanding the backend is the current priority.

### PostgreSQL

PostgreSQL is the initial database choice.

Although CardVault's exact data model has not yet been designed, the information it is expected to manage is structured and contains meaningful relationships. A relational database therefore provides a natural starting point.

PostgreSQL provides the relational capabilities CardVault is likely to need, including constraints, transactions, indexing, and expressive SQL querying, while integrating naturally with Rails through Active Record.

### Docker

Docker will initially be used to run the PostgreSQL server during local development.

This provides an isolated and reproducible local database environment without requiring the PostgreSQL server itself to be installed directly on the development machine.

Docker is a choice about **how PostgreSQL is run locally**, not the reason PostgreSQL itself was selected.

## 9. Alternatives Considered

The initial technology choices were made by considering CardVault's requirements, the goals of the project, and the tradeoffs of reasonable alternatives. The selected technologies are not intended to be universally better; they currently make sense for this project.

### API-Only Rails vs. Full-Stack Rails

Full-stack Rails could provide both the backend and a browser-based user interface within a single application. This could simplify development by keeping the backend and initial graphical interface within the same Rails application.

API-only Rails was selected because CardVault is currently intended to be a backend-focused project, while the eventual client remains undecided.

The tradeoff is that a graphical interface will require a separate client. If that separation eventually creates complexity without providing a meaningful benefit, the decision can be revisited.

### Relational vs. Non-Relational Database

A non-relational database such as Cassandra was considered as an alternative to using a relational database.

Non-relational databases can be valuable when requirements favor characteristics such as very large-scale distributed storage, high write throughput, horizontal scaling, or access patterns that do not fit naturally into a relational model.

CardVault does not currently present those requirements. Its expected information is structured and likely to contain meaningful relationships between concepts such as cards, collectible versions, and collection information. Although the exact model has not yet been designed, relationships and data integrity are expected to be important.

A relational database therefore provides a more natural starting point, including support for relationships, constraints, transactions, joins, and flexible SQL queries.

The tradeoff is that relational databases are not specifically designed around the distributed storage and horizontal-scaling characteristics offered by systems such as Cassandra. CardVault currently has no identified need for those capabilities, so introducing that additional complexity would not solve a current problem.

The database architecture can be reevaluated if CardVault's requirements change significantly.

### PostgreSQL vs. SQLite

Once a relational database was selected, both PostgreSQL and SQLite were reasonable options.

SQLite would provide a simpler development environment because it is embedded and does not require running a separate database server. Rails also supports SQLite directly.

PostgreSQL was selected because CardVault provides an opportunity to work directly with a relational database server while using SQL, relationships, constraints, transactions, and indexing. It integrates well with Rails and provides room for more complex relational data requirements if they emerge.

The tradeoff is additional local-development complexity. Unlike SQLite, PostgreSQL requires a running database server and associated configuration.

### Dockerized PostgreSQL vs. Locally Installed PostgreSQL

PostgreSQL could be installed and run directly on the development machine. This would avoid introducing Docker into the local database environment and would be a valid development approach.

CardVault will instead initially run PostgreSQL through Docker. This keeps the database server isolated from the host system, makes its version and configuration explicit, and makes the local database environment easier to reproduce. It also provides practical experience working with containers during backend development.

The tradeoff is another infrastructure layer to understand and operate.

Docker is being used because its isolation and reproducibility are useful to this project, not because PostgreSQL requires Docker.

### Monolithic Application vs. Microservices

An alternative architecture would be to divide CardVault into multiple independently deployable services responsible for different areas of the system.

Microservices can be valuable when parts of a system need to scale, deploy, or evolve independently or when separate teams need clear ownership of different services.

CardVault does not currently have those requirements. The initial application has a small scope, a single developer, and closely related functionality. Splitting it into multiple services would introduce additional deployment, networking, observability, testing, and data-consistency concerns without addressing an identified need.

CardVault will therefore begin as a single Rails application. If the project later develops components with genuinely independent scaling, deployment, or operational requirements, this decision can be reevaluated.

### Manual Card Entry vs. External Card-Data Integration

CardVault could begin by depending on an external source of One Piece card information and allowing cards to be selected from an existing catalog.

That approach could reduce manual entry and enable capabilities that depend on knowing about cards outside my collection. However, it would also make the initial application dependent on identifying and integrating with a sufficiently complete and reliable external data source.

CardVault will instead begin with manual card entry. This allows the application to solve my immediate collection-management problem without making an external catalog a prerequisite.

The tradeoff is that entering a new collectible version requires more effort and CardVault will initially know only about card information that has been entered into it.

If a suitable external data source is identified later, automatic card-data retrieval or synchronization can be evaluated.

## 10. Open Questions

Some questions intentionally remain open because CardVault has not yet reached a point where answering them would affect development decisions.

These include:

- What kind of user-facing client should eventually be built?
- How should CardVault eventually be hosted and deployed?
- How should its production database be hosted?
- If external card data is introduced later, which source should be used and how should CardVault interact with it?
- Which filtering capabilities prove most useful once I begin using CardVault?
- Which additional collection capabilities become valuable after using the initial version?

These questions should be addressed when the project reaches a point where the answers affect actual development decisions.

## 11. Project Principles

CardVault should begin by solving the real collection-management problem that inspired the project.

The first useful version should prioritize the capabilities I actually need rather than attempting to support every possible One Piece TCG feature or the needs of every collector.

Product decisions should be informed by actually using CardVault. Features can be added, changed, or removed as I learn what makes the application useful.

Technical decisions should be made when there is enough information to evaluate their requirements and tradeoffs meaningfully. Early decisions can be revisited when requirements change or new information becomes available.

Technologies, infrastructure, and features should be introduced because they address an identified project need rather than simply because they are available.

## 12. References

The following resources were used to understand the One Piece Card Game and the collection characteristics relevant to CardVault.

**Official One Piece Card Game**

https://en.onepiece-cardgame.com

The *One Piece Card Game Official Rule Manual, Version 1.11* was used as the primary reference for official card categories, card information, and game terminology.

**Collector-Oriented Reference**

https://www.psacard.com/info/tcg/one-piece-collecting-basics-guide

The PSA guide was used as a secondary reference for understanding collector-oriented distinctions between different printings and versions of One Piece cards.
