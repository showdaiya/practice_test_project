# 基本設計

## 1. 構成
- Web(App) 層 /  層【ステートレス】 / DB / 外部決済PSP / メールサービス
- 依存はAPI経由。内部コンポーネントは以下:
  - CheckoutService, CouponService, InventoryService, SlotService, PaymentService

## 2. 状態と整合性
- 注文状態: `pending → confirmed → canceled`
- 予約状態: `active → consumed | expired`
- 在庫は「確定時減算」。予約は `slot_reservations` に保持

## 3. 凳稿性
- `/checkout/confirm` は `idempotency_key` 必須。キー単位で最初の応答を保存し、以後同一応答を返す

## 4. エラー規約
- 409: 在庫競合/ スロット競合
- 422: クーポン適用不可（ルール異等）

## 5. テスタビリティ/可観模性
- 全APIで `trace_id` を受け受け及び発行
- 監査ログ: actor_id, action, decision, idempotency_key, error_code
- 重要指標: p50/p95/p99、エラー率、予約失敗率、二重計金検出=0
