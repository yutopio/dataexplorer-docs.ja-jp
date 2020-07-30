---
title: activity_engagement プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの activity_engagement プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cdee53ad7f46aacb71b8a8277e5b875e60438874
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349824"
---
# <a name="activity_engagement-plugin"></a>activity_engagement プラグイン

変化するタイムライン ウィンドウを対象に、ID 列に基づいてアクティビティ エンゲージメント率が計算されます。

activity_engagement プラグインを使用して、DAU/WAU/MAU (毎日/毎週/毎月のアクティビティ) を計算できます。

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

## <a name="syntax"></a>構文

*T* `| evaluate` `activity_engagement(` *idcolumn* `,` *TimelineColumn* `,` [*開始* `,` *終了* `,` ] *inneractivitywindow* `,` *outeractivitywindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引数

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: (省略可能) 分析の開始期間の値を持つスカラー。
* *End*: (省略可能) 分析終了期間の値を含むスカラー。
* *Inneractivitywindow*: 内部スコープの分析ウィンドウ期間の値を持つスカラー。
* *Outeractivitywindow*: 外部スコープの分析ウィンドウ期間の値を持つスカラー。
* *dim1*、 *dim2*、...: (省略可能) アクティビティメトリックスの計算をスライスするディメンション列の一覧です。

## <a name="returns"></a>戻り値

(内部スコープウィンドウ内の個別の ID 値のカウント、外側のスコープウィンドウ内での ID 値の個別カウント、アクティビティの比率) を含むテーブルを、内部スコープウィンドウの各期間と、既存の各ディメンションの組み合わせごとに返します。

出力テーブルスキーマは次のとおりです。

|TimelineColumn|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|型: as of *TimelineColumn*|long|long|double|..|..|..|


## <a name="examples"></a>例

### <a name="dauwau-calculation"></a>DAU/WAU の計算

次の例では、ランダムに生成されたデータに対して DAU/WAU (1 日あたりのアクティブユーザー/週あたりのアクティブユーザー比率) を計算します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="活動エンゲージメント dau wau":::

### <a name="daumau-calculation"></a>DAU/MAU の計算

次の例では、ランダムに生成されたデータに対して DAU/WAU (1 日あたりのアクティブユーザー/週あたりのアクティブユーザー比率) を計算します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d)
| project _day, Dau_Mau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="活動エンゲージメント dau mau":::

### <a name="daumau-calculation-with-additional-dimensions"></a>追加ディメンションを使用した DAU/MAU の計算

次の例では、追加のディメンション () を使用して、ランダムに生成されたデータに対して DAU/WAU (1 日あたりのアクティブユーザー/週単位のアクティブユーザー比率) を計算し `mod3` ます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
| extend mod3 = strcat("mod3=", id % 3)
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d, mod3)
| project _day, Dau_Mau=activity_ratio*100, mod3 
| render timechart 
```

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="活動エンゲージメント dau mau mod 3":::
