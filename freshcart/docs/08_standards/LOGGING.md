# 監査ログ規約

## 必須フィールド
- timestamp (ISO8601 UTC)
- trace_id
- actor_id?
- action (confirm/reserve/cancel...)
- resource (order:<ID>/slot:<ID>...)
- decision (confirmed/canceled/held/expired/failed)
- error_code?
- idempotency_key?

## 例
`2025-08-20T10:00:00Z trace=abc actor=U123 action=confirm resource=order:O999 decision=confirmed idempotency=IK-xxx`

