# 代表シーケンス（文章仕様）

## 決済確定（成功）
1) Client → POST /checkout/confirm (idempotency_key含む)
2) API: 凳稿性テーブル照伝 → 未登録なら「処理開始」記録
3) SlotService: 予約状態 `active` を確認、期限内であれば占有へ移行
4) InventoryService: 在庫を確定減算
5) PaymentService: 外部PSPにオーソリ/確定（非同期時は一方pending）
6) Orders: `confirmed` に遷移、メール送信
7) 凳稿性に応答スナップショット保存 → 200応答

## 決済確定（再送）
1) 上記 1) と同一 `idempotency_key`
2) 凳稿性テーブルからスナップショットを返す → 200（または同一結果）
