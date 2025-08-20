# テーブル定義【要点】

## orders
- PK: id (string UUID)
- Columns:
  - user_id (nullable)
  - total_amount decimal(12,2) NOT NULL
  - status enum('pending','confirmed','canceled') NOT NULL
  - delivery_slot_id (FK delivery_slots.id) NOT NULL
  - created_at, updated_at
- Index: (user_id, created_at), (status)

## slot_reservations
- PK: id
- Columns: cart_id(FK), delivery_slot_id(FK), expires_at(datetime), status enum('active','expired','consumed')
- Constraints: UNIQUE(cart_id), INDEX(delivery_slot_id,status)

## idempotency_keys
- PK: key
- Columns: endpoint(varchar), first_request_hash(varchar), response_snapshot(json), created_at(datetime)
