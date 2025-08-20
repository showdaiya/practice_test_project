# エラーコード規約

| code                     | http | message                                  | 例外系 |
|--------------------------|------|-------------------------------------------|--------|
| VALIDATION_ERROR         | 400  | 入力値が不正です                           | 入力   |
| AUTH_REQUIRED            | 401  | 認証が必要です                             | 認証   |
| AUTHZ_FORBIDDEN          | 403  | 許可されていません                         | 許可   |
| RESERVATION_CONFLICT     | 409  | 在庫またはスロットの競合が発生しました     | 競合   |
| COUPON_NOT_APPLICABLE    | 422  | クーポンを適用できません                   | 業務   |
| IDEMPOTENT_REPLAY        | 200  | 冨仏リプレイ（同一結果を返却）             | 業務   |
