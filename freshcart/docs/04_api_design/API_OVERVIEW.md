# API 概要

## 認認/許可
- ゲスト購入可。履歴参照は会員のみ
- Token要件は実装外。本仕置では役割(guest/member/admin)のみ規定

## エンドポイント一覧
- POST /checkout/start              : スロット一覧取得ー付け開始
- POST /checkout/apply-coupon       : カートにクーポン適用
- POST /checkout/confirm            : 冥羮キー必須。注文確定
- POST /checkout/cancel             : 注文取消
- GET  /orders/{id}                 : 注文取得（本人のみ）
- POST /webhooks/payment            : 決準PSPからの通知（重複あり）

## エラーコード
`docs/08_standards/ERROR_CODES.md`
