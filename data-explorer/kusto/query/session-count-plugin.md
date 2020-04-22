---
title: session_countプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーsession_countプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ce6ca3be1859aba0a94ae155c60342be3f1ad1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663144"
---
# <a name="session_count-plugin"></a>session_count プラグイン

タイムライン上の ID 列に基づいてセッション数を計算します。

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `,` *Start* `,` `,` *Bin* `,` `,` `,` *IdColumn* `,` *TimelineColumn* `,` *LookBackWindow* *End* *dim1* *dim2* IdColumn タイムラインコラム開始エンドビンルックバックウィンドウ [dim1 dim2.] `session_count(``)`

**引数**

* *T*: 入力表形式の式。
* *IdColumn*: ユーザー アクティビティを表す ID 値を持つ列の名前。 
* *タイムライン列*: タイムラインを表す列の名前。
* *開始*: 分析開始期間の値を持つスカラー。
* *終了*: 分析終了期間の値を持つスカラー。
* *Bin*: セッション分析ステップ期間のスカラー定数値。
* *ルックバックウィンドウ*: セッションルックバック期間を表すスカラー定数値。 id がタイム`IdColumn`ウィンドウ内`LookBackWindow`に表示されている場合、セッションは既存と見なされます。
* セッションカウントの計算をスライスするディメンション列の*dim1* *、dim2*、.:(オプション)リスト。

**戻り値**

各タイムライン期間および既存のディメンションの組み合わせのセッション数の値を持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*タイムライン列*|薄暗い1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|タイプ:*タイムライン列の時点*|..|..|..|long|


**使用例**


例を挙すると、データの確定性を実現します。
- タイムライン: 1 から 10,000 までのランニング番号
- ID: 1 から 50 までのユーザーの ID

`Id`が (タイムライン`Timeline`% ID = 0) の区切りである場合は、特定の`Timeline`スロットに表示されます。

これは、イベントが任意`Id==1``Timeline`のスロットに表示され、イベントが`Id==2`2`Timeline`番目のスロットごとに表示されることを意味します。

ここでは、データの数20行があります:

```kusto
let _data = range Timeline from 1 to 10000 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// Look on few lines of the data
_data
| order by Timeline asc, Id asc
| limit 20
```

|タイムライン|Id|
|---|---|
|1|1|
|2|1|
|2|2|
|3|1|
|3|3|
|4|1|
|4|2|
|4|4|
|5|1|
|5|5|
|6|1|
|6|2|
|6|3|
|6|6|
|7|1|
|7|7|
|8|1|
|8|2|
|8|4|
|8|8|

次の用語でセッションを定義してみましょう: セッション`Id`の時間スロットが 100 タイムスロットの時間枠で少なくとも 1 回表示されている間、アクティブであると見なされるセッションは 41 タイムスロットです。

次のクエリは、上記の定義に従ってアクティブなセッションの数を示します。

```kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart 
```

:::image type="content" source="images/queries/example-session-count.png" alt-text="セッション数の例":::
