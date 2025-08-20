# 結合テストシナリオ

## S-01 正常（会員・クーポン適用・確定）
前提: 在庫あり、スロットheld、クーポンR1
手順: confirm→200
検証: status=confirmed、金額一致、slot consumed、payments.ok

## S-02 在庫不足
前提: 明細の一部が在庫不足
結果: 409 INV_SHORTAGE、DB不変

## S-03 金額不整合
前提: リクエスト合計が再計算と±2円以上乖離
結果: 422 AMOUNT_MISMATCH

## S-04 3DS保留
結果: 202、注文=auth_pending、タイマ登録

## S-05 クーポン期間外
結果: 422 COUP_TIME

## S-06 冪等再送
同一Idempotency-Key再送→初回と同一応答（ヘッダ含む）

