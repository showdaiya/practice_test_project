# テーブル定義（詳細）

## orders
- PK: id(uuid)
- Columns:
  - user_id uuid?
  - total_amount decimal(12,2) not null
  - status enum('pending','confirmed','auth_pending','canceled','fulfilled') not null
  - slot_reservation_id uuid not null
  - created_at, updated_at timestamp not null
- Index: (user_id, created_at desc)
- Check: total_amount >= 0

## order_items
- PK: id
- FK: order_id → orders.id
- Columns: sku_id, qty(int >0), unit_price dec(12,2), tax_rate dec(4,3), discount dec(12,2) default 0
- Index: (order_id), (sku_id)

## inventory
- PK: sku_id
- Columns: quantity int not null, updated_at timestamp
- Check: quantity >= 0

## slot_reservations
- PK: id
- Columns: slot_id, cart_id, status enum('held','consumed','expired'), ttl_at, created_at
- Index: (slot_id), (cart_id), (status, ttl_at)
- Check: ttl_at >= created_at

## idempotency_keys
- PK: key
- Columns: request_hash char(64), response_json jsonb, status enum('fresh','replay'), created_at
- Retention: 24h

## payments
- PK: id
- Columns: order_id, psp_id, amount dec(12,2), status enum('ok','pending','fail'), raw jsonb, created_at
- Index: (order_id), (psp_id)

