---
title: activity_engagementプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのactivity_engagement プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fe6d3f68f59ae52c96d3e9467cc364d792b13cf3
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664027"
---
# <a name="activity_engagement-plugin"></a>activity_engagement プラグイン

変化するタイムライン ウィンドウを対象に、ID 列に基づいてアクティビティ エンゲージメント率が計算されます。

activity_engagementプラグインは、DAU/WAU/ MAU(日/週/月次活動)の計算に使用することができます。

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**構文**

*T* `| evaluate` `,` `,` `,` `,` `,` `,` *Start* `,` *TimelineColumn* `,` *dim1* *IdColumn* *End* *dim2* *OuterActivityWindow* *InnerActivityWindow* IdColumn タイムライン列 [ 開始終了 ] インナーアクティビティウィンドウアウターアクティビティウィンドウ [dim1 dim2.]] `activity_engagement(``)`

**引数**

* *T*: 入力表形式の式。
* *IdColumn*: ユーザー アクティビティを表す ID 値を持つ列の名前。 
* *タイムライン列*: タイムラインを表す列の名前。
* *開始*: (オプション) スカラーと分析開始期間の値。
* *End*: (オプション) スカラーと分析終了期間の値を指定します。
* *内部スコープ分析ウィンドウ*期間の値を持つスカラー。
* *外側スコープ分析ウィンドウ*期間の値を持つスカラー。
* *dim1*、 *dim2*、 .: (オプション) アクティビティ指標の計算をスライスするディメンション列のリスト。

**戻り値**

内部スコープ ウィンドウの各期間と既存のディメンションの組み合わせに対して、テーブル (内部スコープ ウィンドウ内の ID 値の個別のカウント、外側のスコープ ウィンドウ内の ID 値の個別のカウント、およびアクティビティの比率) を持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|タイムライン列|dcount_activities_inner|dcount_activities_outer|activity_ratio|薄暗い1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|タイプ:*タイムライン列の時点*|long|long|double|..|..|..|


**使用例**

### <a name="dauwau-calculation"></a>ダウ/WAU 計算

次の例では、ランダムに生成されたデータに対して DAU/WAU (日次アクティブ ユーザー/週単位アクティブ ユーザー比) を計算します。

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

:::image type="content" source="images/queries/activity-engagement-dau-wau.png" border="false" alt-text="アクティビティ・エンゲージメント・ダウ・ワウ":::

### <a name="daumau-calculation"></a>ダウ/モー計算

次の例では、ランダムに生成されたデータに対して DAU/WAU (日次アクティブ ユーザー/週単位アクティブ ユーザー比) を計算します。

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

:::image type="content" source="images/queries/activity-engagement-dau-mau.png" border="false" alt-text="アクティビティエンゲージメントダウマウ":::

### <a name="daumau-calculation-with-additional-dimensions"></a>追加のディメンションを含む DAU/MAU 計算

次の例では、ディメンションが追加されたランダムに生成されたデータに対して、DAU/WAU (日次アクティブ ユーザー`mod3`/ 週単位のアクティブ ユーザー比率) を計算します ( ) 。

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

:::image type="content" source="images/queries/activity-engagement-dau-mau-mod3.png" border="false" alt-text="アクティビティエンゲージメントダウマウモッズ3":::
