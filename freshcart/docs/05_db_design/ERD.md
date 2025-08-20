# ERD（文章仕様）

- users( id, email?, name?, created_at )
- products( id, name, price, status )
- inventory( sku_id, quantity )
- carts( id, user_id?, created_at ), cart_items( id, cart_id, sku_id, qty, unit_price )
- delivery_slots( id, start_at, end_at, capacity, status )
- slot_reservations( id, cart_id, delivery_slot_id, expires_at, status )
- coupons( id, code, type(enum:amount,rate,free_shipping), value, min_subtotal, combinable(bool), valid_from, valid_to, status )
- orders( id, user_id?, total_amount, status(enum:pending,confirmed,canceled), delivery_slot_id, created_at, updated_at )
- order_items( id, order_id, sku_id, qty, unit_price, discount )
- payments( id, order_id, method, status, psp_tx_id? )
- idempotency_keys( key, endpoint, first_request_hash, response_snapshot, created_at )
- audit_logs( id, actor_id?, action, resource, decision, error_code?, trace_id, created_at )
