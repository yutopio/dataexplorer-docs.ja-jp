---
title: session_count プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの session_count プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0790a3ab173bc653cbd3c4c15b3f28e5a0c70cd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252895"
---
# <a name="session_count-plugin"></a>session_count プラグイン

タイムライン上の ID 列に基づいてセッション数を計算します。

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

## <a name="syntax"></a>構文

*T* `| evaluate` `session_count(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Bin* `,` *look backwindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引数

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: 分析の開始期間の値を使用したスカラー。
* *End*: 分析終了期間の値を使用したスカラー。
* *Bin*: セッション分析ステップ期間のスカラー定数値。
* ルックバック*ウィンドウ*: セッションのルックバック期間を表すスカラー定数値。 の ID が `IdColumn` 内の時間枠に表示される場合 `LookBackWindow` 、セッションは既存のものと見なされます。 ID が表示されない場合、セッションは新しいものと見なされます。
* *dim1*、 *dim2*、...: (省略可能) セッション数の計算をスライスするディメンション列の一覧です。

## <a name="returns"></a>戻り値

各タイムラインの期間と、既存のディメンションの組み合わせごとにセッション数の値を含むテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*TimelineColumn*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|型: as of *TimelineColumn*|..|..|..|long|


## <a name="examples"></a>例

この例では、データは決定的であり、2つの列を含むテーブルを使用します。
- Timeline: 1 ~ 1万の実行数
- Id: 1 ~ 50 のユーザーの Id

`Id``Timeline` `Timeline` (タイムライン% Id = = 0) の区分線の場合は、特定のスロットに表示されます。

を持つイベントは、 `Id==1` 任意の `Timeline` スロット、 `Id==2` 2 番目のスロットごとにイベントを表示 `Timeline` します。

次の20行のデータがあります。

<!-- csl: https://help.kusto.windows.net/Samples -->
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

次に、セッションを定義してみましょう。ユーザー ( `Id` ) が100の時間帯に少なくとも1回、またはセッションの参照元ウィンドウが41の時間帯である限り、セッションはアクティブと見なされます。

次のクエリは、上記の定義に従ってアクティブなセッションの数を示しています。

<!-- csl: https://help.kusto.windows.net/Samples -->
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
