# Audit

## Discrepancy 1 — Reservation cardinality

Початкова модель дозволяла зв'язок `WishlistItem 1:N Reservation` та передбачала
зберігання статусів `active/cancelled`. Це створювало модель історичних
резервувань, хоча основна вимога системи полягає у блокуванні товару лише на
період поточного резервування.

Для спрощення моделі було уточнено вимогу: `Reservation` представляє лише
поточне резервування. Після скасування запис видаляється.

У результаті зв'язок змінено на `WishlistItem 1:0..1 Reservation`,
`Reservation.item_id` зроблено UNIQUE, а атрибут `status` видалено.

**Changed files:**
- `spec.md`
- `er-model.md`

**Commit:**
`lab1: refine reservation requirements and model`

## Discrepancy 2 — User password representation

Початкова специфікація описувала атрибут `password` як дані, що повинні
зберігатися для користувача. Це недостатньо точно описує вимогу до
зберігання облікових даних і може трактуватися як зберігання пароля у
відкритому вигляді.

Вимогу уточнено: система повинна зберігати `password_hash`, а не сам пароль.

У ER-моделі атрибут `password` замінено на `password_hash`.

**Changed files:**
- `spec.md`
- `er-model.md`

**Commit:**
`lab1: clarify password storage requirement`

## Discrepancy 3 — Missing completion state for wishlist items

Початкова модель не містила інформації про те, чи було бажання вже виконане.
Через це модель не дозволяла явно представити стан товару, після якого він
не повинен бути доступним для подальшого резервування.

Вимоги було уточнено: wishlist item може бути позначений як виконаний,
виконане бажання не може бути зарезервоване, а позначати бажання виконаним
може лише власник wishlist.

До `WishlistItem` додано атрибут `is_completed`.

**Changed files:**
- `spec.md`
- `er-model.md`

**Commit:**
`lab1: add wishlist item completion requirement`
