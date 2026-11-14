# CardVault — Phase 1 Requirements and Use Cases

## 1. Purpose and Scope

CardVault is a personal collection-management API for manually recording owned One Piece TCG cards, viewing the collection, searching by card name, adjusting owned quantities, and removing records.

The MVP supports five card categories: **Leader, Character, Event, Stage, and DON!!**. It has no user authentication, frontend, or external card catalog. Card data is entered manually.

The requirements below describe observable behavior. Database schema design, Rails architecture, and implementation details will be addressed in a subsequent project phase.

**Primary actor — Collector:** The sole collector is the project owner, using an API client such as Postman to manage their own collection. The Collector submits requests to CardVault; CardVault validates them, reads or updates saved records, and returns HTTP/JSON responses. There are no additional user roles or automated external actors in the MVP.

## 2. Domain Terminology

| Term | Definition |
|---|---|
| **Collectible record** | A saved card identity, its applicable metadata, and the number of owned copies |
| **Record ID** | System-generated identifier used to retrieve, adjust, or delete a collectible record |
| **Category** | Gameplay category: Leader, Character, Event, Stage, or DON!! |
| **Card number** | Printed identifier for a numbered card, e.g., `OP01-001` |
| **Rarity** | Printed rarity classification, distinct from artwork/printing variant |
| **Variant** | A supported printing variation; each collectible has exactly one |
| **Printing Description** | Collector-entered description identifying a DON!! design |
| **Quantity** | Number of owned copies represented by a collectible record |

## 3. Business Rules

### BR-01 — Collection scope

Only English-language One Piece TCG cards are in scope. The API validates manually entered data according to the rules below; it does not independently verify cards against an external catalog.

### BR-02 — Record completeness

A collectible record must satisfy all fields required for its category. A record must not be partially saved when validation fails. No draft records are supported.

### BR-03 — Category-specific fields

**Legend:** ✅ Required · ◯ When present on the card · — Not applicable

| Field | Leader | Character | Event | Stage | DON!! |
|---|:---:|:---:|:---:|:---:|:---:|
| Name | ✅ | ✅ | ✅ | ✅ | — |
| Card Number | ✅ | ✅ | ✅ | ✅ | — |
| Category | ✅ | ✅ | ✅ | ✅ | ✅ |
| Rarity | ✅ | ✅ | ✅ | ✅ | — |
| Block Symbol | ✅ | ✅ | ✅ | ✅ | — |
| Color(s) | ✅ | ✅ | ✅ | ✅ | — |
| Type | ✅ | ✅ | ✅ | ✅ | — |
| Cost | — | ✅ | ✅ | ✅ | — |
| Power | ✅ | ✅ | — | — | — |
| Attribute | ✅ | ✅ | — | — | — |
| Life | ✅ | — | — | — | — |
| Counter | — | ◯ | — | — | — |
| Variant | ✅ | ✅ | ✅ | ✅ | ✅ |
| Quantity | ✅ | ✅ | ✅ | ✅ | ✅ |
| Printing Description | — | — | — | — | ✅ |

**Field-specific requirements**

- **Required free-text fields:** Name, Card Number, Type, Attribute, and Printing Description (where applicable) must contain at least one non-whitespace character. Trim leading and trailing whitespace before validation and storage; preserve internal spaces. Empty and whitespace-only values are rejected.

- **Card number:** Must be nonempty. No fixed card-number pattern is enforced.
- **Colors:** Represented as a JSON array containing at least one allowed color for Leader, Character, Event, and Stage. Multiple distinct colors are allowed; duplicate colors and unknown colors are rejected. Example: `{"colors":["Red","Green"]}`.
- **Type:** Stored as one text value reflecting the printed Type, including any slash-separated entries (e.g., `Supernovas/Heart Pirates`).
- **Block symbol:** Must be a string containing a positive integer (such as `"1"`, `"2"`, or `"10"`) or the special value `"X"`. Zero, negative numbers, decimals, other nonnumeric strings, and JSON numbers are rejected.
- **Cost, Power, and Life:** Must be non-negative JSON integers. Zero is allowed; negative values, decimals, and numeric strings are rejected.
- **Counter:** When present for a Character card, must be a non-negative JSON integer. Zero is allowed; negative values, decimals, and numeric strings are rejected. An omitted or explicitly `null` Counter represents absence; numeric `0` is distinct from `null`.
- **Field applicability:** Fields marked as not applicable (`—`) must not be supplied for that category, including when their value is `null`. Such requests are rejected with `422 Unprocessable Content` and errors naming the offending fields.
- **Missing and null values:** Required fields must be present and non-null; the optional Character Counter follows the exception defined above.
- **DON!!:** Printing Description must be nonempty and may describe a character, clothing, accessories, or another identifying visual element. No separate Leader association is required.
- **Effects and trigger effects:** Not stored in Phase 1.

### BR-04 — Predefined values

The following fields accept only predefined values:

| Field | Allowed values | Default |
|---|---|---|
| Category | `Leader`, `Character`, `Event`, `Stage`, `DON!!` | None |
| Color | `Red`, `Green`, `Blue`, `Purple`, `Black`, `Yellow` | None |
| Rarity | `C`, `UC`, `R`, `SR`, `SEC`, `L`, `P` | None |
| Variant | `Standard`, `Alternate Art (Parallel)`, `Special Rare (SP)`, `Manga Rare`, `Treasure Rare (TR)`, `Manga Event`, `Golden DON!!` | `Standard` |

- CardVault supports **seven predefined card variants** in Phase 1.
- Exactly one variant is associated with each collectible record.
- Rarity and variant are independent fields.
- Category-specific field applicability is defined in BR-03. **Golden DON!!** is valid only for DON!! records. DON!! records accept only `Standard` or `Golden DON!!` as their variant; Leader, Character, Event, and Stage records cannot use `Golden DON!!`.
- Fields such as Name, Card Number, Type, and Printing Description accept collector-entered values subject to BR-03; they are not predefined enumerations.

### BR-05 — Identity, uniqueness, and normalization

| Category | Unique identity combination |
|---|---|
| Leader, Character, Event, Stage | Card Number + Variant |
| DON!! | Printing Description + Variant |

- Card Number and Printing Description are compared **case-insensitively**, ignoring leading and trailing whitespace.
- Surrounding whitespace is trimmed before storage; entered capitalization is preserved for display.
- For numbered cards, all records with the same normalized Card Number must have the same normalized Name and Category, regardless of Variant. Name comparisons are case-insensitive and ignore leading and trailing whitespace; the entered capitalization is preserved for display. A conflicting Name or Category is rejected with `422 Unprocessable Content` without changing saved records.
- Creating an existing identity is rejected. Quantity must be changed through the adjustment operation.
- Uniqueness and numbered-card Name/Category consistency must hold under concurrent create requests. If two requests simultaneously create the same identity, exactly one may succeed with `201 Created`; the other must receive `422 Unprocessable Content` with a duplicate-identity error. Concurrent conflicting Name/Category requests must not both succeed.
- The MVP does not distinguish physical printings that share the same identity combination.

### BR-06 — Quantity at creation

Quantity defaults to `1` when omitted. An explicitly supplied quantity must be a **JSON integer greater than zero**. Numeric strings, decimals, zero, and negative values are rejected.

### BR-07 — Quantity adjustment

An adjustment must be a **nonzero JSON integer**, positive or negative. Numeric strings and decimals are rejected.

- Quantity adjustments must be **atomic**: concurrent successful requests must each apply exactly once to the latest committed quantity, without lost updates. Validation of the resulting quantity and any deletion at zero must occur as part of that atomic operation; rejected adjustments must not change stored data.
- Each successfully processed adjustment request is a separate operation, even when its body is identical to another request. The MVP does not detect accidental retries or accept idempotency keys; automatic retry deduplication is deferred.

| Resulting quantity | Required behavior |
|---|---|
| Greater than zero | Save the updated quantity |
| Exactly zero | Delete the collectible record |
| Less than zero | Reject the request; preserve the original record |

### BR-08 — Metadata immutability

Phase 1 does not provide an operation for editing card metadata. To correct metadata, the collector deletes the existing record and creates a replacement. Quantity adjustment is the only supported update.

### BR-09 — Validation and data integrity

- Reject unsupported query parameters on collection browsing and searching with `400 Bad Request` rather than silently ignoring them. Only `search` is supported on `GET /api/v1/cards`.
- Reject unknown request-body fields rather than silently ignoring them. For quantity adjustments, `adjustment` is the only permitted request-body field; it is required, non-null, and must be a nonzero JSON integer.
- Reject recognized fields that do not apply to the selected card category, even if explicitly set to `null`.
- Reject missing or `null` values for required fields. Optional Counter may be omitted or explicitly `null`.
- Reject values outside predefined enumerations and category-incompatible variants, with descriptive errors identifying the invalid value and category.
- Return **all detected validation errors** in one JSON `errors` array. Each unknown or inapplicable field must be identified by its key name.
- Reject invalid card-data and quantity requests with `422 Unprocessable Content`, without partial writes or unintended changes to existing records. Malformed JSON and invalid or unsupported collection query parameters instead return `400 Bad Request` as specified in Section 4.
- Unexpected service/database failures must not be returned as successful responses or empty collections. They must be logged for local debugging without leaking internal implementation details to the client.

## 4. API Response Conventions

| Operation / outcome | HTTP status | Response |
|---|---|---|
| Create collectible | `201 Created` | Complete saved record, including generated ID |
| Browse or search | `200 OK` | JSON array; `[]` if no results |
| Retrieve collectible | `200 OK` | Complete saved record |
| Adjust quantity; resulting quantity above zero | `200 OK` | Complete updated record |
| Adjust quantity; resulting quantity equals zero | `200 OK` | JSON removal confirmation |
| Delete collectible directly | `204 No Content` | No response body |
| Individual record does not exist | `404 Not Found` | Descriptive JSON error |
| Invalid card data or quantity | `422 Unprocessable Content` | JSON validation errors |
| Invalid or unsupported collection query parameter | `400 Bad Request` | Descriptive JSON error in an `errors` array |
| Malformed JSON request body | `400 Bad Request` | Descriptive JSON error in an `errors` array |
| Database unavailable or unexpected server failure | `500 Internal Server Error` | Generic JSON error in an `errors` array; log details locally |

**Failure-response convention:** Error responses use the JSON shape `{"errors":["Description of the problem"]}`. Malformed JSON receives `400` with a descriptive message; unexpected database/server failures receive `500` with a generic message that does not expose SQL, credentials, stack traces, or other internal details. Unexpected failures are logged locally. A database failure must never be converted to `200 OK` with `[]`.

**Complete-record response convention:** Responses containing a Character record always include the `counter` key. When the Character has no printed Counter value, return `"counter": null`; otherwise return its non-negative integer value (including `0`). Fields inapplicable to a category are omitted from that category’s record. This applies to creation, browsing, searching, retrieval, and successful quantity adjustment.

**Example validation error**

```json
{
  "errors": [
    "Name is required",
    "Rarity is invalid",
    "Quantity must be greater than zero",
    "Unknown field: quanity",
    "Field 'cost' is not applicable to Leader cards"
  ]
}
```

**Example missing-record error**

```json
{
  "errors": ["Card not found in your collection"]
}
```

## 5. API Use Cases and Acceptance Criteria

The paths below are the proposed Phase 1 API contract and will be formalized in `docs/openapi.yaml`. **Primary actor for UC-01 through UC-06:** Collector (the project owner). The Collector uses an API client such as Postman; no graphical frontend is required. Business rules BR-01 through BR-09 and the response conventions in Section 4 apply throughout.

### UC-01 — Create a collectible

**Endpoint:** `POST /api/v1/cards`
**Goal:** Record an owned collectible version that is not already saved.
**Preconditions:** CardVault is running and its PostgreSQL database is available; the Collector has the information required for the selected category.
**Trigger:** The Collector wants to add a collectible to their collection.

**Main success flow**
1. The Collector sends a `POST` request with card category, required category-specific metadata, and any optional supported fields.
2. CardVault checks that the request uses recognized fields and that all supplied fields apply to the selected category.
3. CardVault validates required fields, data types, allowed values, and category–variant compatibility under BR-03 and BR-04.
4. CardVault applies the defaults for omitted quantity and variant, normalizes applicable text, and checks both collectible identity uniqueness and numbered-card Name/Category consistency under BR-05.
5. CardVault saves exactly one complete collectible record.
6. CardVault responds with `201 Created` and the full saved record, including its generated ID and quantity.

**Alternative flows**
- If quantity is omitted, CardVault uses `1`.
- If variant is omitted, CardVault uses `Standard`.
- Different supported variants of the same numbered card may be saved as separate records.
- If the collectible is already saved, the Collector may instead use UC-02 to change its quantity; the create request itself does not redirect or update the existing record.

**Exception flows**
- Missing, null, blank, incorrectly typed, or invalid required data: `422 Unprocessable Content`.
- Unknown fields, fields inapplicable to the category, or incompatible category–variant combinations: `422`.
- An identity already present in the collection: `422`.
- A numbered Card Number already associated with a different Name (after case-insensitive, surrounding-whitespace normalization) or Category: `422`.
- Concurrent creation of an identical identity: exactly one request succeeds with `201`; the other returns `422` with a duplicate-identity error. Concurrent creations must not violate Name/Category consistency.
- Errors are returned in the JSON `errors` array, aggregating all detected validation errors.

**Postconditions:** On success, exactly one new complete record exists. On rejection, no new record is saved and existing records are unchanged.

**Acceptance criteria**
- All five categories can be created with their applicable metadata and defaults.
- Different variants of one numbered card coexist only when their normalized Name and Category agree.
- Under concurrent creation, duplicate identities and conflicting numbered-card associations cannot be persisted.

### UC-02 — Adjust collectible quantity

**Endpoint:** `PATCH /api/v1/cards/{id}/quantity`
**Goal:** Increase or decrease the number of owned copies of an existing collectible without re-entering its metadata.
**Preconditions:** CardVault and PostgreSQL are available; the record identified by `{id}` exists.
**Trigger:** The Collector acquires or removes copies of a collectible.

**Example request**
```json
{"adjustment": -2}
```

**Main success flow**
1. The Collector identifies the saved record and sends a `PATCH` request containing only a nonzero integer `adjustment`.
2. CardVault locates the record and validates the request body.
3. CardVault atomically calculates the proposed quantity against the latest committed quantity, ensuring concurrent successful adjustments are not lost.
4. If the result is positive, CardVault saves the new quantity without changing the collectible's metadata.
5. CardVault responds with `200 OK` and the complete updated record.

**Alternative flows**
- A positive adjustment increases quantity; a negative adjustment decreases it.
- If the calculated quantity is exactly zero, CardVault deletes the record and returns `200 OK` with `{"message":"Card removed from collection","quantity":0}`.

**Exception flows**
- An unknown record ID returns `404 Not Found` with the missing-record error.
- A missing, null, zero, non-integer, or incorrectly typed adjustment returns `422`.
- Any extra request-body field returns `422`.
- An adjustment producing a negative quantity returns `422` and leaves the record unchanged, including when other adjustments are processed concurrently.
- Identical adjustment requests are not automatically recognized as retries; each successfully processed request is applied separately.

**Postconditions:** On a positive result, only the saved quantity changes; on zero, the record no longer exists; on failure, the record remains unchanged.

**Acceptance criteria**
- Adjustments change only quantity, never card metadata.
- Concurrent adjustments preserve every successful change, including correct zero-result deletion and below-zero rejection.
- Rejected adjustments do not modify stored data.

### UC-03 — Browse the collection

**Endpoint:** `GET /api/v1/cards`
**Goal:** Review all saved collectibles and owned quantities in a predictable order.
**Preconditions:** CardVault and PostgreSQL are available.
**Trigger:** The Collector wants to inspect the collection without a search term.

**Main success flow**
1. The Collector sends a `GET /api/v1/cards` request without a `search` parameter.
2. CardVault retrieves all saved collectible records across all five categories.
3. CardVault orders numbered cards by Card Number ascending and uses the fixed variant priority for records sharing a card number.
4. CardVault places DON!! records after numbered cards, ordering them by Printing Description alphabetically **without regard to case**, and Standard before Golden DON!! when descriptions match.
5. CardVault returns `200 OK` with one flat JSON array of complete records and quantities.

**Alternative flows**
- If the collection contains no records, CardVault returns `200 OK` with `[]`.
- If multiple records share a card number, their variants appear in this order: Standard → Alternate Art (Parallel) → Special Rare (SP) → Treasure Rare (TR) → Manga Event → Manga Rare.

**Exception flows**
- Unsupported query parameters (for example, `?rarity=SR`) return `400 Bad Request` with a descriptive JSON `errors` array; they are not silently ignored.
- Unexpected service or database outages return `500` with a generic JSON error, not successful empty results; details are logged locally.

**Postconditions:** No collection data is modified.

**Acceptance criteria**
- All five categories appear in one flat array of complete applicable records.
- Numbered-card and DON!! ordering follows the specified rules, including case-insensitive DON!! descriptions.
- Browsing is unpaginated in the MVP.

### UC-04 — Search by card name

**Endpoint:** `GET /api/v1/cards?search=luffy`
**Goal:** Find saved numbered collectibles whose names contain a supplied term.
**Preconditions:** CardVault and PostgreSQL are available.
**Trigger:** The Collector wants to check which matching cards and variants are already owned.

**Main success flow**
1. The Collector sends a `GET /api/v1/cards` request with a nonblank `search` parameter.
2. CardVault trims leading and trailing whitespace from the search term, then validates that the normalized term contains at least one non-whitespace character.
3. CardVault searches the Name field of saved records using case-insensitive partial matching.
4. CardVault sorts matching records using the same order as UC-03.
5. CardVault returns `200 OK` with the complete matching records and quantities.

**Alternative flows**
- A term matching multiple collectible variants returns each matching saved record.
- If no saved Name matches, CardVault returns `200 OK` with `[]`.
- If the `search` parameter is absent, the request follows UC-03 instead.

**Exception flows**
- If `search` is present but empty or whitespace-only, CardVault returns `400 Bad Request` with a descriptive JSON error.
- Any unsupported query parameter, whether supplied alone or alongside `search`, returns `400 Bad Request` with a descriptive JSON `errors` array.

**Postconditions:** No collection data is modified.

**Acceptance criteria**
- Search matches only saved numbered-card Names using trimmed, case-insensitive partial terms.
- Every matching variant is returned in UC-03 order; DON!! Printing Description is excluded.

### UC-05 — Retrieve a collectible

**Endpoint:** `GET /api/v1/cards/{id}`
**Goal:** Inspect the full saved information and owned quantity for one collectible.
**Preconditions:** CardVault and PostgreSQL are available.
**Trigger:** The Collector requests a specific saved record by ID.

**Main success flow**
1. The Collector sends a `GET` request using the collectible's record ID.
2. CardVault locates the record.
3. CardVault returns `200 OK` with all saved applicable fields, including ID, variant, and quantity.

**Alternative flows**
- Any of the five supported categories can be retrieved through the same endpoint; the returned fields reflect that category.

**Exception flows**
- If the ID does not identify an existing record, CardVault returns `404 Not Found` with `{"errors":["Card not found in your collection"]}`.

**Postconditions:** No collection data is modified.

**Acceptance criteria**
- Retrieving any supported category by a known ID returns its complete applicable metadata and current quantity.

### UC-06 — Delete a collectible

**Endpoint:** `DELETE /api/v1/cards/{id}`
**Goal:** Remove an entire collectible record, independently of how many copies it represents.
**Preconditions:** CardVault and PostgreSQL are available; the identified record exists.
**Trigger:** The Collector decides to remove a record, including when correcting metadata under BR-08.

**Main success flow**
1. The Collector sends a `DELETE` request containing the record ID in the path.
2. CardVault locates the saved record.
3. CardVault deletes the record regardless of its quantity.
4. CardVault responds with `204 No Content` and no response body.

**Alternative flows**
- If the Collector is correcting metadata, they may subsequently create a replacement record using UC-01; this is a separate request, not an automatic edit or replacement.

**Exception flows**
- An unknown or already-deleted record ID returns `404 Not Found` with the missing-record error.

**Postconditions:** On success, the selected record no longer exists and unrelated records are unchanged; on failure, no record is deleted.

**Acceptance criteria**
- Deletion removes the selected record regardless of quantity without changing unrelated records.

## 6. Data Persistence Requirements

### PR-01 — Data Persistence

**Requirement:** CardVault must persist successfully committed collection records and quantities in PostgreSQL using a persistent Docker volume.

**Expected behavior**
- Successfully created records remain available after restarting Rails or the PostgreSQL container.
- Saved records and quantities survive normal Mac restarts and application updates that retain the existing database volume.
- Successfully committed quantity adjustments remain reflected in the collection after restart.
- Records intentionally removed through quantity adjustment (UC-02) or direct deletion (UC-06) remain absent after restart.
- Database unavailability must not be presented as an empty collection or a successful operation; failures follow the API error conventions in Section 4.

**Limitations**
- Destructive removal of the persistent database volume, disk failure, and restoration from backup are outside the MVP persistence guarantee.
- Automated backups and disaster recovery are out of scope.

**Acceptance criteria**
1. Records created through UC-01 remain available after normal Rails and PostgreSQL container restarts.
2. Saved quantities remain accurate after normal Rails, PostgreSQL container, and Mac restarts, and application updates retaining the database volume.
3. Deleted records do not reappear after restart.
4. PostgreSQL data resides in persistent storage, not solely in the container's ephemeral filesystem.

## 7. Testing Requirements

Use **RSpec** for automated model and request tests. For each API use case and PR-01, cover its principal successful behavior, relevant failures, boundary conditions, and data-integrity expectations without duplicating every assertion at every test layer.

**Required automated test scenarios** include:

1. Create a valid record in each of the five categories.
2. Reject missing, unknown, and category-inapplicable fields.
3. Reject invalid predefined values and category–variant combinations.
4. Reject duplicate collectible identities.
5. Adjust quantities upward, downward, to zero, and below zero.
6. Verify browsing, case-insensitive Name search with trimmed surrounding whitespace, case-insensitive DON!! sorting, unsupported query parameter rejection, and unknown-record responses.
7. Verify rejected requests leave persisted data unchanged.
8. Verify numbered cards sharing a normalized Card Number cannot have conflicting Names or Categories across variants; case-only and surrounding-whitespace Name differences are equivalent.
9. Verify concurrent creation cannot produce duplicate identities or inconsistent Name/Category associations; concurrent quantity adjustments do not lose successful updates and enforce zero/deletion and negative-result boundaries atomically.
10. Verify PR-01 persistence across normal restarts, including saved quantities and deleted records.
11. Verify malformed JSON returns `400` in the standard `errors` format and simulated database failures return a generic `500` JSON error rather than an empty success result; unexpected failures are logged locally.

These scenarios are a minimum coverage checklist, not an exhaustive list of individual tests.

Use a **Postman collection** for manual API verification.

Continuous integration is outside Phase 1 scope.

## 8. Runtime, Deployment, and Operational Expectations

- **Runtime environment:** The Phase 1 MVP runs locally on the Collector’s macOS computer. The Rails API runs on the host, while PostgreSQL runs in a Docker Compose-managed container.
- **User interaction:** The Collector accesses the API using a client such as Postman. A graphical frontend is not required.
- **Database availability:** PostgreSQL must be accessible for collection reads and writes; failures follow the response and logging conventions in Section 4.
- **Persistence:** Persistent storage and restart guarantees are defined in PR-01 (Section 6).
- **Deployment:** Cloud hosting and production deployment are not required for the Phase 1 MVP. No externally accessible service is expected.
- **Operations:** The Collector is responsible for starting the local application and database as needed. Automated backups, production monitoring, and disaster recovery are deferred; destructive volume deletion and disk failure are outside the persistence guarantee.

These expectations describe the MVP operating environment, not a commitment to a future hosting architecture.

## 9. Out of Scope

- Frontend, authentication, multiple collectors, and deployment/hosting.
- Editing metadata, zero-quantity placeholders, quantity-change history, idempotency keys, and automatic retry deduplication.
- Search by card number or DON!! description; category/rarity filters; custom sorting; pagination.
- Effects and trigger-effect text; images/scanning; external catalog synchronization.
- Price tracking, condition/grading, wishlists, and analytics.
- Automated backups, CI, Swagger UI integration, and generated API documentation.
- Distinguishing printings with the same MVP identity combination.

---

**Reference materials:** One Piece Card Game Official Rule Manual v1.11; [The One Piece TCG Rarity System, Explained](https://www.tcgplayer.com/content/article/The-One-Piece-TCG-Rarity-System-Explained/03a91348-97ba-47df-bf6b-d46352616f98/).
