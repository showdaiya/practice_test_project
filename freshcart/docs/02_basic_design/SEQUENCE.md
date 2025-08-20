# 代表シーケンス（詳細）

## /checkout/confirm（注文確定）
前提: カート有効、スロット予約held、Idempotency-Key必須

1. 冪等照会: `idempotency_keys` にキー一致があれば保存済み応答を返却（HTTP 200/202）。
2. 入力検証: スキーマ/必須項目/ユーザー権限。
3. T1開始（READ COMMITTED）。必要レコードに限定して `FOR UPDATE`。
4. 金額再計算: 明細合計→クーポン（D-01/D-04）→送料→税。
5. スロット検証: `slot_reservations` を `FOR UPDATE` で取得。TTL・状態=held・cart一致。
6. 在庫引当: `inventory` をSKUごとに `FOR UPDATE`。不足で T1中断→409 `INV_SHORTAGE`。
7. 注文作成: `orders(pending)`、`order_items` 追加。
8. T1コミット（外部I/Oはトランザクション外に出す）。
9. PSP決済呼出: timeout=3s, retry=2, 冪等ヘッダ付、ネットワーク例外は再試行。
10. PSP応答正規化（D-03）。
11. T2開始。
12. 分岐:
    - 成功: `orders.status=confirmed`、`slot_reservations→consumed`、`payments` 登録。
    - 失敗: `orders.status=canceled`、在庫戻し、`slot_reservations→active`。
    - 保留: `orders.status=auth_pending`、タイマー登録（30分）。
13. `idempotency_keys` にリクエストハッシュと最終応答保存（status=fresh/replay）。
14. T2コミット、レスポンス返却。
15. 非同期: 監査ログ出力、メール通知、メトリクス。

例外:
- PSPタイムアウト: 504 `PSP_TIMEOUT`、再試行ジョブへ。
- 金額不整合: 422 `AMOUNT_MISMATCH`（許容±1円超）。
- スロット期限: 409 `SLOT_EXPIRED`。

## /slots/reserve（予約）
1. スロット行を `FOR UPDATE SKIP LOCKED` で取得。
2. 残容量>0 なら `held` 作成（TTL=15分）、capacity-1。
3. 同一カートheldありは再利用。

