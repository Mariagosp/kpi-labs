# Wishlist Service — ER Model

## Overview

This project defines a conceptual Entity-Relationship (ER) model
for a Wishlist Service — a web service that allows users to create
and share wishlists containing products they would like to receive.

Other users can reserve items from public wishlists to indicate
that they intend to fulfill those wishes.

The model focuses on the structure of the domain data, relationships
between entities, cardinalities, primary keys, foreign keys,
and business constraints.

## Domain

The selected domain is wishlist management and item reservation.

The system supports the following main concepts:

- Users who create and manage wishlists.
- Wishlists that contain items users would like to receive.
- Wishlist items that describe desired products.
- Reservations that allow other users to reserve items from public
  wishlists.

The model also accounts for completed wishes, privacy of wishlists,
and restrictions on reserving one's own items.

## Project Scope

This project covers conceptual data modeling only.

It does not include SQL DDL, ORM models, application implementation,
or physical database configuration.

The ER model is described declaratively using Mermaid.

## Project Artifacts

- [Specification](spec.md) — entities, attributes, relationships,
  business constraints, and acceptance criteria.
- [ER Model](er-model.md) — declarative Mermaid ER model.
- [Audit](audit.md) — identified discrepancies and corresponding
  specification and model changes.
- [Defense](DEFENSE.md) — rationale for the selected domain,
  modeling decisions, normalization, and consistency verification.
- [Architecture Decision Record](adr/001-reservation-model.md) —
  documented modeling decision.

## Main Entities

| Entity | Description |
|---|---|
| `User` | A registered user of the service. |
| `Wishlist` | A list of wishes owned by a user. |
| `WishlistItem` | An item included in a wishlist. |
| `Reservation` | A current reservation of a wishlist item by a user. |

## Key Modeling Decisions

- All entity identifiers use the UUID type.
- Each wishlist belongs to exactly one user.
- Each wishlist item belongs to exactly one wishlist.
- A wishlist item can have at most one current reservation.
- A reservation is deleted when it is cancelled; reservation history
  is outside the scope of the current model.
- Passwords are represented by `password_hash`, not plaintext passwords.
- Completed wishlist items cannot be reserved.

## Validation

The model is checked against the specification to verify:

- Entity and attribute consistency.
- Primary and foreign key correctness.
- Relationship cardinalities.
- Identifier type consistency.
- Business constraint coverage.
- Avoidance of unnecessary data duplication.

See [Audit](audit.md) for the recorded discrepancies and changes.
