---
title: active_users_count プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの active_users_count プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75f1c92dfb76c56894d1f38dec24a31690f3f789
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349841"
---
# <a name="active_users_count-plugin"></a>active_users_count プラグイン

個別の値の数を計算します。各値は、少なくとも、少なくとも最小数の期間に表示されます。

"ファン以外" の種類を含まない、"ファン" の個別のカウントを計算する場合に便利です。 ユーザーは、"ファン" としてカウントされます。これは、元の状態の間にアクティブだった場合に限ります。 ルックバック期間は、ユーザーが検討され `active` ている ("ファン") かどうかを判断するためにのみ使用されます。 集計自体には、[元に戻す] ウィンドウのユーザーは含まれません。 これに対して、 [sliding_window_counts](sliding-window-counts-plugin.md)集計は、ルックバック期間のスライディングウィンドウで実行されます。

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

## <a name="syntax"></a>構文

*T* `| evaluate` `active_users_count(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *LookbackWindow* `,` *Period* `,` *ActivePeriodsCount* `,` *Bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引数

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: (省略可能) 分析の開始期間の値を持つスカラー。
* *End*: (省略可能) 分析終了期間の値を含むスカラー。
* ルック*バックウィンドウ*: ユーザーの外観がチェックされる期間を定義する、スライド式の時間枠。 元に戻す期間は、([現在の外観]-[ルックバックウィンドウ]) から始まり、([現在の外観]) で終わります。 
* *期間*: スカラー定数 timespan。1つの外観としてカウントされます (ユーザーは、この timespan の少なくとも distinct ActivePeriodsCount に出現すると、アクティブとしてカウントされます。
* *ActivePeriodsCount*: ユーザーがアクティブかどうかを判断するための、個別のアクティブ期間の最小数。 アクティブなユーザーとは、少なくとも以上のアクティブな期間に出現したユーザーのことです。
* *Bin*: 分析ステップ期間のスカラー定数値。 には、数値/日付/時刻/タイムスタンプ値、またはの文字列を指定でき `week` / `month` / `year` ます。 すべての期間は、対応する[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)関数になります。
* *dim1*、 *dim2*、...: (省略可能) アクティビティメトリックスの計算をスライスするディメンション列の一覧です。

## <a name="returns"></a>戻り値

ActivePeriodCounts に表示された Id の個別のカウント値を持つテーブルを返します。次の期間、各タイムラインの期間、および既存の各ディメンションの組み合わせです。

出力テーブルスキーマは次のとおりです。

|*TimelineColumn*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|型: as of *TimelineColumn*|..|..|..|long|


## <a name="examples"></a>例

過去8日間に少なくとも3つの異なる日に出現した個別のユーザーの数を計算します。 分析期間: 2018 年7月。

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

|Timestamp|`dcount`|
|---|---|
|2018-07-01 00:00: 00.0000000|1|
|2018-07-15 00:00: 00.0000000|1|

次の両方の条件を満たしている場合、ユーザーはアクティブと見なされます。 
* ユーザーは、少なくとも3つの異なる日 (Period = 1d、ActivePeriods = 3) で表示されていました。
* ユーザーは、現在の外観を含めて、8d のルックバックウィンドウで表示されていました。

次の図では、この条件によってアクティブになっているのは、7/20 のユーザー A と7/4 のユーザー B のみです (上記のプラグインの結果を参照してください)。 ユーザー B の表示は7/4 の [元に戻す] ウィンドウには含まれますが、6/29-30 の開始終了時間の範囲には含まれません。 

:::image type="content" source="images/queries/active-users-count.png" alt-text="アクティブなユーザーカウントの例":::
