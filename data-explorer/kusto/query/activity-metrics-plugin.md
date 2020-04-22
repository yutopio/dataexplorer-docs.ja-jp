---
title: activity_metricsプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーactivity_metricsプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fe199636d23f576220772b3a33c96ec8367a1e23
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663929"
---
# <a name="activity_metrics-plugin"></a>activity_metrics プラグイン

現在の期間ウィンドウと前の期間のウィンドウに基づいて、有用なアクティビティメトリック (個別のカウント値、新しい値の個別のカウント、保持率、およびチャーン率) を計算します (各タイムウィンドウが*以前の時間枠と*比較される[activity_counts_metricsプラグイン](activity-counts-metrics-plugin.md)とは異なります)。

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `,` `,``,` `,` *IdColumn* `,` `,` *Start* `,` *End* *Window* *TimelineColumn* *dim2* *dim1* IdColumn タイムライン列 [ 開始終了 ] ウィンドウ [ dim1 dim2 ..] `activity_metrics(``)`

**引数**

* *T*: 入力表形式の式。
* *IdColumn*: ユーザー アクティビティを表す ID 値を持つ列の名前。 
* *タイムライン列*: タイムラインを表す列の名前。
* *開始*: (オプション) スカラーと分析開始期間の値。
* *End*: (オプション) スカラーと分析終了期間の値を指定します。
* *ウィンドウ*: 分析ウィンドウ期間の値を持つスカラー。 数値/日時/タイムスタンプ値、またはの 1 つである[startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md)`week`/`month`/`year`文字列を指定できます。 
* *dim1*、 *dim2*、 .: (オプション) アクティビティ指標の計算をスライスするディメンション列のリスト。

**戻り値**

各タイムライン期間および既存のディメンションの組み合わせについて、個別のカウント値、新しい値の個別のカウント、保持率、および解約率を持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*タイムライン列*|dcount_values|dcount_newvalues|retention_rate|churn_rate|薄暗い1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|タイプ:*タイムライン列の時点*|long|long|double|double|..|..|..|

**メモ**

***保持レートの定義***

`Retention Rate`期間にわたって次のように計算されます。

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

は`# of customers returned during the period`、次のように定義されます。

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`0.0 から 1.0 まで変化する  
スコアが高いほど、ユーザーの返品量が多くなります。


***解約率の定義***

`Churn Rate`期間にわたって次のように計算されます。
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

は`# of customer lost in the period`、次のように定義されます。

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`0.0 から 1.0 まで変化するスコアは、より多くのユーザーがサービスに戻らないということを意味します。

***チャーンとリテンション率***

`Churn Rate`および`Retention Rate`の定義から派生した、 は常に次のようになります。

    [Retention rate] = 100.0% - [Churn Rate]


**使用例**

### <a name="weekly-retention-rate-and-churn-rate"></a>週単位のリテンション率とチャーン率

次のクエリでは、週の 1 週間のウィンドウの保持率とチャーン率を計算します。

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate, churn_rate
| render timechart 
```

|_day|retention_rate|churn_rate|
|---|---|---|
|2017-01-02 00:00:00.0000000|(NaN)|(NaN)|
|2017-01-09 00:00:00.0000000|0.179910044977511|0.820089955022489|
|2017-01-16 00:00:00.0000000|0.744374437443744|0.255625562556256|
|2017-01-23 00:00:00.0000000|0.612096774193548|0.387903225806452|
|2017-01-30 00:00:00.0000000|0.681141439205955|0.318858560794045|
|2017-02-06 00:00:00.0000000|0.278145695364238|0.721854304635762|
|2017-02-13 00:00:00.0000000|0.223172628304821|0.776827371695179|
|2017-02-20 00:00:00.0000000|0.38|0.62|
|2017-02-27 00:00:00.0000000|0.295519001701645|0.704480998298355|
|2017-03-06 00:00:00.0000000|0.280387770320656|0.719612229679344|
|2017-03-13 00:00:00.0000000|0.360628154795289|0.639371845204711|
|2017-03-20 00:00:00.0000000|0.288008028098344|0.711991971901656|
|2017-03-27 00:00:00.0000000|0.306134969325153|0.693865030674847|
|2017-04-03 00:00:00.0000000|0.356866537717602|0.643133462282398|
|2017-04-10 00:00:00.0000000|0.495098039215686|0.504901960784314|
|2017-04-17 00:00:00.0000000|0.198296836982968|0.801703163017032|
|2017-04-24 00:00:00.0000000|0.0618811881188119|0.938118811881188|
|2017-05-01 00:00:00.0000000|0.204657727593507|0.795342272406493|
|2017-05-08 00:00:00.0000000|0.517391304347826|0.482608695652174|
|2017-05-15 00:00:00.0000000|0.143667296786389|0.856332703213611|
|2017-05-22 00:00:00.0000000|0.199122325836533|0.800877674163467|
|2017-05-29 00:00:00.0000000|0.063468992248062|0.936531007751938|

:::image type="content" source="images/queries/activity-metrics-churn-and-retention.png" border="false" alt-text="アクティビティー・メトリックのチャーンと保持":::

### <a name="distinct-values-and-distinct-new-values"></a>個別値と個別の '新規' 値 

次のクエリでは、週の 1 週間のウィンドウで個別の値と 'new' 値 (前のタイム ウィンドウに表示されない ID) が計算されます。


```kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// 
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, dcount_values, dcount_newvalues
| render timechart 
```

|_day|dcount_values|dcount_newvalues|
|---|---|---|
|2017-01-02 00:00:00.0000000|630|630|
|2017-01-09 00:00:00.0000000|738|575|
|2017-01-16 00:00:00.0000000|1187|841|
|2017-01-23 00:00:00.0000000|1092|465|
|2017-01-30 00:00:00.0000000|1261|647|
|2017-02-06 00:00:00.0000000|1744|1043|
|2017-02-13 00:00:00.0000000|1563|432|
|2017-02-20 00:00:00.0000000|1406|818|
|2017-02-27 00:00:00.0000000|1956|1429|
|2017-03-06 00:00:00.0000000|1593|848|
|2017-03-13 00:00:00.0000000|1801|1423|
|2017-03-20 00:00:00.0000000|1710|1017|
|2017-03-27 00:00:00.0000000|1796|1516|
|2017-04-03 00:00:00.0000000|1381|1008|
|2017-04-10 00:00:00.0000000|1756|1162|
|2017-04-17 00:00:00.0000000|1831|1409|
|2017-04-24 00:00:00.0000000|1823|1164|
|2017-05-01 00:00:00.0000000|1811|1353|
|2017-05-08 00:00:00.0000000|1691|1246|
|2017-05-15 00:00:00.0000000|1812|1608|
|2017-05-22 00:00:00.0000000|1740|1017|
|2017-05-29 00:00:00.0000000|960|756|

:::image type="content" source="images/queries/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="アクティビティー・メトリックの dcount および dcount 新しい値":::
