# API 概要（詳細）

- 認証: Bearer（練習用途のため任意）
- 役割: guest/member/admin
- 書込系は `Idempotency-Key` 必須（8+文字）

## 共通
- リクエストヘッダ: `Idempotency-Key`, `X-Trace-Id`
- エラーフォーマット: `{ code, message, details? }`
- エラーコード: `08_standards/ERROR_CODES.md` 参照

## エンドポイント
|Path|Method|概要|
|--|--|--|
|/checkout/confirm|POST|注文確定|
|/slots/reserve|POST|配送スロット予約|
|/coupons/validate|POST|クーポン検証|

## 冪等の扱い
- 同一キーは**同一応答**を返す（正常・保留を含む）。
- 失敗系の再送は方針で分岐（PAY_FAILは再送不可、PSP_TIMEOUTは再送可）。

