# Defense — Wishlist Service ER Model

## 1. Domain Selection

The selected domain is a Wishlist Service — a web service for
creating and sharing wishlists and reserving desired items.

This domain was chosen because it represents a practical system
with several related entities and meaningful business constraints.

The main concepts are users, wishlists, wishlist items, and
reservations. Their relationships demonstrate one-to-many and
one-to-zero-or-one cardinalities.

The domain also includes constraints related to privacy, ownership,
reservation uniqueness, and completed wishes. These requirements
make it possible to evaluate whether the ER model accurately
represents the rules of the system.

The scope is limited to conceptual data modeling. Application
implementation, SQL DDL, and ORM models are not included.

## 2. Main Modeling Decisions

### 2.1 User

The `User` entity represents a registered user.

Its primary key is `user_id`, which uses the UUID type.
The `email` attribute is unique because one email address must
not identify multiple users.

The `password_hash` attribute represents a stored password hash
rather than a plaintext password.

### 2.2 Wishlist

The `Wishlist` entity represents a list of wishes owned by a user.

The relationship between `User` and `Wishlist` is one-to-many:
one user can own multiple wishlists, while each wishlist belongs
to exactly one user.

The `is_public` attribute represents whether a wishlist can be
accessed by other users for reservation purposes.

### 2.3 WishlistItem

The `WishlistItem` entity represents a desired product included
in a wishlist.

Each item belongs to exactly one wishlist. A wishlist can contain
multiple items.

The `is_completed` attribute indicates whether the wish has been
fulfilled. According to the specification, a completed item cannot
be reserved.

Product information such as the name, description, price,
product URL, and image URL is stored with the wishlist item.

### 2.4 Reservation

The `Reservation` entity represents a current reservation made
by a user for a wishlist item.

A wishlist item can have no reservation or one current reservation.
The `item_id` foreign key must therefore be unique among reservations.

A user can make multiple reservations, but each reservation belongs
to exactly one user.

The model does not preserve cancelled reservations. Cancelling
a reservation removes its record, allowing the item to be reserved
again.

Reservation history is outside the scope of the current model.

## 3. Relationships and Cardinalities

The model contains the following relationships:

| Relationship | Cardinality | Explanation |
|---|---|---|
| `User` — `Wishlist` | 1:N | One user can own multiple wishlists; each wishlist has one owner. |
| `Wishlist` — `WishlistItem` | 1:N | One wishlist can contain multiple items; each item belongs to one wishlist. |
| `User` — `Reservation` | 1:N | One user can make multiple reservations; each reservation belongs to one user. |
| `WishlistItem` — `Reservation` | 1:0..1 | An item can have no current reservation or one current reservation. |

The cardinalities are based on the business requirements in
`spec.md`.

No separate associative entity is introduced for a many-to-many
relationship because the current requirements do not define
a direct many-to-many relationship that needs to be represented
without additional attributes.

The `Reservation` entity is retained because a reservation has
its own attributes, including `reservation_id` and `reserved_at`,
and represents a meaningful business event.

## 4. Keys and Identifier Types

All entity identifiers use UUID, as specified in `spec.md`.

The model contains the following primary keys:

- `User.user_id`
- `Wishlist.wishlist_id`
- `WishlistItem.item_id`
- `Reservation.reservation_id`

Foreign keys represent the following ownership and reservation
relationships:

- `Wishlist.user_id` references `User.user_id`.
- `WishlistItem.wishlist_id` references `Wishlist.wishlist_id`.
- `Reservation.item_id` references `WishlistItem.item_id`.
- `Reservation.user_id` references `User.user_id`.

The `User.email` attribute is unique.

The `Reservation.item_id` attribute must also be unique to ensure
that a wishlist item has at most one current reservation.

These constraints are part of the conceptual model's requirements.
This project does not implement them through SQL DDL.

## 5. Normalization

The model separates users, wishlists, wishlist items, and reservations
into distinct entities because they represent different concepts
with different attributes and relationships.

User information is stored in `User`, rather than repeated in every
wishlist or reservation.

Wishlist information is stored in `Wishlist`, rather than repeated
for every item belonging to that wishlist.

Item information is stored in `WishlistItem`, while reservation
information is stored in `Reservation`.

This separation reduces unnecessary duplication and makes the
relationships between the domain concepts explicit.

The specification requires the model to avoid obvious data
duplication and satisfy the intended normalization requirements.

## 6. Business Constraints

The model is designed around the following business rules:

- Each wishlist has exactly one owner.
- Each wishlist item belongs to exactly one wishlist.
- Only items from public wishlists can be reserved.
- A user cannot reserve their own wishlist item.
- An item cannot have more than one current reservation.
- Cancelled reservations do not block future reservations.
- Completed wishlist items cannot be reserved.
- Only the wishlist owner can mark an item as completed.
- Completing a reserved item cancels its current reservation.
- The price of a wishlist item cannot be negative.

Some of these rules concern application behavior and authorization.
They cannot be fully represented by entity attributes and
relationship cardinalities alone.

They are therefore documented in `spec.md` as business constraints
and must be considered when validating the model.

## 7. Audit and Corrections

The audit identified three discrepancies between the initial
requirements and the intended domain model.

### 7.1 Reservation Cardinality

**Problem**

The initial model allowed multiple reservation records for the same
wishlist item and included reservation statuses such as `active`
and `cancelled`.

This represented reservation history, although the intended scope
was to store only a current reservation.

**Correction**

The specification was clarified so that a reservation represents
only the current reservation. A cancelled reservation is deleted.

The relationship between `WishlistItem` and `Reservation` was
changed to one-to-zero-or-one, and `Reservation.item_id` was
required to be unique. The `status` attribute was removed.

### 7.2 Password Representation

**Problem**

The initial specification used the attribute name `password`,
which did not clearly express that plaintext passwords must not
be stored.

**Correction**

The attribute was renamed to `password_hash` to represent a stored
password hash.

**Files changed**

- `spec.md`
- `er-model.md`

**Commit**

`lab1: clarify password storage requirement`

### 7.3 Wishlist Item Completion

**Problem**

The initial model did not represent whether a wishlist item had
already been fulfilled.

As a result, the requirements could not distinguish completed
items from items that were still available for reservation.

**Correction**

The `is_completed` attribute was added to `WishlistItem`.

The specification was also clarified to state that completed items
cannot be reserved, only the wishlist owner can mark an item as
completed, and completing a reserved item cancels its reservation.

## 8. Consistency Verification

The ER model was compared with `spec.md`.

The verification covers:

- Presence of all four specified entities.
- Consistency of entity and attribute names.
- Presence of all primary keys.
- Presence of all specified foreign keys.
- Consistent use of UUID identifiers.
- Correct relationship cardinalities.
- Representation of the unique email requirement.
- Representation of the one-current-reservation constraint.
- Representation of completed wishlist items.
- Absence of unnecessary associative entities.
- Consistency between the declarative model and the rendered diagram.

The three corrections described in the audit were incorporated
into the specification and ER model rather than being applied
only as manual changes to the rendered diagram.

The rendered ER diagram must be regenerated from the current
declarative model after the final changes.

Business constraints involving permissions, reservation eligibility,
and cancellation behavior also require verification against the
written specification because they cannot all be expressed by
the ER diagram alone.

## 9. Conclusion

The project defines a conceptual ER model for a Wishlist Service.

The model distinguishes users, wishlists, wishlist items, and current
reservations. Its relationships and keys are intended to reflect
the business rules documented in `spec.md`.

The audit records three changes made to improve the consistency
and completeness of the requirements and the model.

The final acceptance condition is that the specification,
declarative ER model, and rendered diagram remain consistent.
