# 状態機械定義

## M-01 注文(Order)
状態: pending → confirmed → fulfilled / canceled  
保留: auth_pending

| 事象 | 現在 | 遷移先 | 実装メモ |
|--|--|--|--|
| confirm成功 | pending | confirmed | 決済OK |
| PSP失敗 | pending | canceled | 在庫返却 |
| 3DS保留 | pending | auth_pending | タイマー30分 |
| 追加認証OK | auth_pending | confirmed | |
| 期限切れ | auth_pending | canceled | 自動キャンセル |
| 出荷完了 | confirmed | fulfilled | 追跡番号必須 |
| キャンセル可 | confirmed | canceled | D-05適用 |

## M-02 スロット予約
状態: active → held → consumed / expired

| 事象 | 現在 | 遷移先 | 実装メモ |
|--|--|--|--|
| 予約作成 | active | held | capacity-1 |
| 決済確定 | held | consumed | order紐付け |
| 決済失敗 | held | active | capacity+1 |
| TTL超過 | held | expired | 自動解放（ジョブ） |

