# ADR 001: Model current reservations without reservation history

## Status

Accepted

## Context

The system needs to prevent multiple users from reserving the same wishlist
item at the same time.

The initial model represented `WishlistItem → Reservation` as 1:N and used
a `status` attribute to distinguish active and cancelled reservations.

## Decision

`Reservation` represents only the current reservation of a wishlist item.

- `WishlistItem → Reservation` is `1:0..1`;
- `Reservation.item_id` is UNIQUE;
- `Reservation` does not contain `status`;
- cancelling a reservation removes the `Reservation` record.

## Consequences

The model is simpler and directly represents the current availability of an
item. However, the system does not preserve historical reservation records.
