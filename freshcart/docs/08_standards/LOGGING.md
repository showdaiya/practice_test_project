# 監守ログ規約

必須: timestamp, trace_id, actor_id?, action, resource, decision, error_code?, idempotency_key?  
例: `2025-08-20T10:00:00Z TRACE=abc actor=U123 action=confirm resource=order:O999 decision=confirmed idempotency=IK-xxx`
