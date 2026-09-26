```mermaid
erDiagram
    USER ||--o{ WISHLIST : "creates"
    WISHLIST ||--o{ WISHLIST_ITEM : "contains"
    USER ||--o{ RESERVATION : "makes"
    WISHLIST_ITEM ||--o| RESERVATION : "has"

    USER {
        uuid user_id PK
        varchar_100 name
        varchar_255 email UK
        varchar_255 password_hash
    }

    WISHLIST {
        uuid wishlist_id PK
        uuid user_id FK
        varchar_100 name
        text description
        boolean is_public
    }

    WISHLIST_ITEM {
        uuid item_id PK
        uuid wishlist_id FK
        varchar_100 name
        text description
        numeric_10_2 price
        text product_url
        text image_url
    }

    RESERVATION {
        uuid reservation_id PK
        uuid item_id FK UK
        uuid user_id FK
        timestamp reserved_at
    }
```
