---
title: active_users_countプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーactive_users_countプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f324507d1a4528c5efefc14f7820437383211ca6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519348"
---
# <a name="active_users_count-plugin"></a>active_users_countプラグイン

ルックバック期間内の少なくとも最小期間内に各値が出現した値の個別のカウントを計算します。

「ファン以外」の出現を含まない一方で、「ファン」の個別のカウントを計算するのに便利です。 ユーザーは、ルックバック期間中にアクティブだった場合にのみ「ファン」としてカウントされます。 ルックバック期間は、ユーザーが「ファン」と見な`active`されるかどうかを判断するためにのみ使用されます。 集計自体には、ルックバック ウィンドウのユーザーは含まれません ([sliding_window_counts] (sliding-window-counts-plugin.md) とは異なり、集計がルックバック期間のスライディング ウィンドウ上にある場合)。

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Period* `,` *Bin* *IdColumn* `,` `,` `,` `,` *TimelineColumn* `,` *LookbackWindow* `,` *dim2* *ActivePeriodsCount* *dim1* IdColumn タイムライン列開始終了ルックバックウィンドウ期間アクティブ期間カウントビン [dim1 dim2.] `active_users_count(``)`

**引数**

* *T*: 入力表形式の式。
* *IdColumn*: ユーザー アクティビティを表す ID 値を持つ列の名前。 
* *タイムライン列*: タイムラインを表す列の名前。
* *開始*: (オプション) スカラーと分析開始期間の値。
* *End*: (オプション) スカラーと分析終了期間の値を指定します。
* *ルックバックウィンドウ*: ユーザーの外観がチェックされる期間を定義するスライディング時間枠。 ルックバック期間は([現在の外観] - [ルックバックウィンドウ])で始まり、上で終了します([現在の外観])。 
* *Period*: 単一の外観としてカウントするスカラー定数の期間 (この期間の少なくとも別個の ActivePeriodsCount に表示される場合、ユーザーはアクティブとしてカウントされます)。
* *アクティブ期間数*: ユーザーがアクティブかどうかを判断する、個別のアクティブ期間の数は最小限です。 アクティブ ユーザーとは、少なくとも (同等以上) のアクティブ期間に出現したユーザーです。
* *Bin*: 分析ステップ期間のスカラー定数値。 数値/日時/タイムスタンプ値、またはの 1 つである[startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md)`week`/`month`/`year`文字列を指定できます。
* *dim1*、 *dim2*、 .: (オプション) アクティビティ指標の計算をスライスするディメンション列のリスト。

**戻り値**

ルックバック期間内、各タイムライン期間、および既存のディメンションの組み合わせに対して ActivePeriodCounts に表示された ID の個別のカウント値を持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*タイムライン列*|薄暗い1|..|dim_n|dcount_values|
|---|---|---|---|---|
|タイプ:*タイムライン列の時点*|..|..|..|long|


**使用例**

少なくとも3日間に3日前の8日間に出現した個別のユーザーの週単位の金額を計算します。 分析期間:2018年7月

```kusto
let Start = datetime(2018-07-01);
let End = datetime(2018-07-31);
let LookbackWindow = 8d;
let Period = 1d;
let ActivePeriods = 3;
let Bin = 7d; 
let T =  datatable(User:string, Timestamp:datetime)
[
    "B",      datetime(2018-06-29),
    "B",      datetime(2018-06-30),
    "A",      datetime(2018-07-02),
    "B",      datetime(2018-07-04),
    "B",      datetime(2018-07-08),
    "A",      datetime(2018-07-10),
    "A",      datetime(2018-07-14),
    "A",      datetime(2018-07-17),
    "A",      datetime(2018-07-20),
    "B",      datetime(2018-07-24)
]; 
T | evaluate active_users_count(User, Timestamp, Start, End, LookbackWindow, Period, ActivePeriods, Bin)



```

|Timestamp|dcount|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

ユーザーは、少なくとも 3 日間 (Period = 1d、ActivePeriods =3) で、現在の外観 (現在の外観を含む) 以前の 8d のルックバック ウィンドウで、アクティブと見なされます。 下の図では、この基準に従ってアクティブになっている唯一の外観は、7/20のユーザAと7/4のユーザB(上記のプラグインの結果を参照)です。 6/29-30 のユーザー B の表示は開始終了時間範囲に含まれていませんが、7/4 のユーザー B のルックバック ウィンドウに含まれることに注意してください。 

![alt text](images/queries/active-users-count.png "アクティブ ユーザー数")