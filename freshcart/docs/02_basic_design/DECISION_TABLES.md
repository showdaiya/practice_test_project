# 決定表（短文セルのみ／根拠付き）

本システムの主要分岐を決定表で統一管理する。セルは短文。長文は備考に集約。

## D-01 クーポン適用可否
前提: 1注文1クーポン。割引は税抜合計に対して適用し、その後消費税を計算。

### 条件
|ID|条件|値の定義|
|--|--|--|
|C1|クーポン状態|active/inactive|
|C2|期間内|y/n|
|C3|最低購入金額以上|y/n （税抜小計 >= min_amount）|
|C4|カテゴリ適合|y/n （全明細が許可カテゴリ内）|
|C5|併用可|y/n|
|C6|全体利用上限未到達|y/n|
|C7|ユーザー利用上限未到達|y/n|
|C8|ユーザー状態|normal/blocked|
|C9|地域対象内|y/n|

### 規則
|R|C1|C2|C3|C4|C5|C6|C7|C8|C9|結果|理由|
|--|--|--|--|--|--|--|--|--|--|--|--|
|R1|active|y|y|y|y|y|y|normal|y|適用|OK|
|R2|active|y|n|*|*|*|*|*|*|不可|COUP_MIN|
|R3|active|y|y|n|*|*|*|*|*|不可|COUP_CAT|
|R4|active|y|y|y|n|*|*|*|*|不可|COUP_STACK|
|R5|active|y|y|y|y|n|*|*|*|不可|COUP_CAP|
|R6|active|y|y|y|y|y|n|*|*|不可|COUP_USER|
|R7|active|n|*|*|*|*|*|*|*|不可|COUP_TIME|
|R8|active|y|y|y|y|y|y|blocked|*|不可|USER_BLOCK|
|R9|active|y|y|y|y|y|y|normal|n|不可|COUP_AREA|
|R10|inactive|*|*|*|*|*|*|*|*|不可|COUP_OFF|

> 備考: `*` は無関係。結果「適用」は割引額を丸めルール T-01 に従って確定。

---

## D-02 配送スロット確保
楽観ロック前提、SKIP LOCKED 併用可（DB別の実装注意）。

### 条件
|ID|条件|値の定義|
|--|--|--|
|C1|スロット状態|active/closed|
|C2|予約期限内|y/n（now <= slot.end - safety_margin）|
|C3|残容量|gt0/eq0|
|C4|同一カート既存予約|y/n（未消費のheldが存在）|

### 規則
|R|C1|C2|C3|C4|結果|理由|
|--|--|--|--|--|--|--|
|R1|active|y|gt0|n|予約確定(held)|SLOT_OK|
|R2|active|y|gt0|y|再利用|SLOT_REUSE|
|R3|active|y|eq0|*|満席|SLOT_FULL|
|R4|active|n|*|*|期限切れ|SLOT_TIME|
|R5|closed|*|*|*|受付終了|SLOT_CLS|

> 備考: 再利用は同一 `cart_id` の `held` を返すだけ（重複作成しない）。

---

## D-03 決済結果マッピング（PSP→注文状態）
### 条件
|ID|条件|値|
|--|--|--|
|C1|PSP結果|ok/pending/fail|
|C2|3DS追加認証要否|y/n|
|C3|冪等再送|y/n（同一Idempotency-Key一致）|

### 規則
|R|C1|C2|C3|注文状態|HTTP|コード|
|--|--|--|--|--|--|--|
|R1|ok|n|*|confirmed|200|-|
|R2|pending|y|*|auth_pending|202|NEED_3DS|
|R3|fail|*|*|payment_fail→cancel|402|PAY_FAIL|
|R4|*|*|y|保存済み応答再送|200/202|-|

---

## D-04 金額計算・丸め（税/送料/割引）
優先順: 小計→クーポン→送料→税（外税）

### 条件
|ID|条件|値|
|--|--|--|
|C1|小計合計|number|
|C2|クーポン種別|percent/fixed|
|C3|送料|number|
|C4|税率|number（例: 0.10）|

### 規則（丸めは各行で四捨五入、最終合計は±1円乖離許容）
|R|C2|割引額|課税対象|合計|
|--|--|--|--|--|
|R1|percent|round(C1*率)|C1-割引+送料|total = 課税対象 + round(課税対象*税率)|
|R2|fixed|min(C1,額)|C1-割引+送料|同上|

超過誤差: `|計算合計 - リクエスト合計| > 1円` なら `AMOUNT_MISMATCH(422)`。

---

## D-05 キャンセル可否
|ID|条件|値|
|--|--|--|
|C1|注文状態|pending/confirmed/auth_pending/fulfilled/canceled|
|C2|出荷状況|not_shipped/shipped|
|C3|期限内|y/n（購入後X分以内）|

|R|C1|C2|C3|結果|理由|
|--|--|--|--|--|--|
|R1|pending|*|*|可|未確定|
|R2|confirmed|not_shipped|y|可|ポリシー内|
|R3|confirmed|shipped|*|不可|発送済|
|R4|auth_pending|*|y|可|保留解除|
|R5|fulfilled|*|*|不可|完了|
|R6|canceled|*|*|不可|既キャンセル|

