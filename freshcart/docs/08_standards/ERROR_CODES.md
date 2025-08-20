# エラーコード（HTTP対応）

| code | http | message |
|--|--:|--|
| AMOUNT_MISMATCH | 422 | 金額が不整合です |
| INV_SHORTAGE | 409 | 在庫が不足しています |
| SLOT_EXPIRED | 409 | スロットの有効期限が切れています |
| SLOT_FULL | 409 | スロットが満席です |
| COUP_MIN | 422 | 最低購入金額に達していません |
| COUP_CAT | 422 | 対象外カテゴリが含まれます |
| COUP_STACK | 422 | クーポンの併用はできません |
| COUP_CAP | 409 | クーポンの利用上限に達しています |
| COUP_USER | 409 | ユーザーの利用上限に達しています |
| COUP_TIME | 422 | クーポンの適用期間外です |
| COUP_OFF | 410 | クーポンは無効です |
| COUP_AREA | 422 | 対象地域外です |
| USER_BLOCK | 403 | ユーザーは利用停止です |
| NEED_3DS | 202 | 追加認証が必要です |
| PAY_FAIL | 402 | 決済に失敗しました |
| PSP_TIMEOUT | 504 | 決済システムがタイムアウトしました |

