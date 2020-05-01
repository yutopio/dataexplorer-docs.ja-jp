---
title: session_count プラグイン-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの session_count プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7ebbbc401f8fdee79aaa328d45c7758d9acb931e
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619051"
---
# <a name="session_count-plugin"></a>session_count プラグイン

タイムライン上の ID 列に基づいてセッション数を計算します。

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` *End* `,` *dim2* *Start* `,` *dim1* *TimelineColumn* `,` *IdColumn* `,` *Bin* `,` *LookBackWindow* idcolumn`,` TimelineColumn`,` Start`,` Bin look backwindow [dim1 dim2...] `session_count(``)`

**引数**

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: 分析の開始期間の値を使用したスカラー。
* *End*: 分析終了期間の値を使用したスカラー。
* *Bin*: セッション分析ステップ期間のスカラー定数値。
* ルックバック*ウィンドウ*: セッションのルックバック期間を表すスカラー定数値。 から`IdColumn`の id が内`LookBackWindow`の時間枠に表示される場合、セッションは既存のものと見なされます。そうでない場合、セッションは新しいものと見なされます。
* *dim1*、 *dim2*、...: (省略可能) セッション数の計算をスライスするディメンション列の一覧です。

**戻り値**

各タイムラインの期間と、既存のディメンションの組み合わせごとにセッション数の値を含むテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*TimelineColumn*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|型: as of *TimelineColumn*|..|..|..|long|


**使用例**


この例では、データを決定的にするために、2つの列を含むテーブルを作成します。
- Timeline: 1 ~ 1万の実行数
- Id: 1 ~ 50 のユーザーの Id

`Id`(タイムライン% `Timeline` Id = = 0) の区分`Timeline`線の場合は、特定のスロットに表示されます。

これは、を持つ`Id==1`イベントは任意`Timeline`のスロット、2番`Id==2`目`Timeline`のスロットごとにイベントが表示されることを意味します。

次の20行のデータがあります。

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

次に、セッションを定義してみましょう。ユーザー (`Id`) が100の時間帯に少なくとも1回、またはセッションの参照元ウィンドウが41の時間帯である限り、セッションはアクティブと見なされます。

次のクエリは、上記の定義に従ってアクティブなセッションの数を示しています。

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

:::image type="content" source="images/session-count-plugin/example-session-count.png" alt-text="セッション数の例" border="false":::
