---
title: activity_metrics プラグイン-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの activity_metrics プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8106d419f20dcacdec6386294a5b9ffb8d1bc8e2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225906"
---
# <a name="activity_metrics-plugin"></a>activity_metrics プラグイン

は、現在の期間ウィンドウと前の期間ウィンドウに基づいて (個別のカウント値、新しい値の個別の数、保持率、およびチャーン率)、(すべての時間ウィンドウが以前の*すべて*の時間枠と比較される) [activity_counts_metrics プラグイン](activity-counts-metrics-plugin.md)とは異なり、有用なアクティビティメトリックを計算します。

```kusto
T | evaluate activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `activity_metrics(` *idcolumn* `,` *TimelineColumn* `,` [*開始* `,` *終了* `,` ]*ウィンドウ*[ `,` *dim1* `,` *dim2* `,` ...]`)`

**引数**

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: (省略可能) 分析の開始期間の値を持つスカラー。
* *End*: (省略可能) 分析終了期間の値を含むスカラー。
* *ウィンドウ*: 分析ウィンドウ期間の値を持つスカラー。 には、数値/日付/時刻/タイムスタンプ値、またはのいずれかの文字列を指定でき `week` / `month` / `year` ます。この場合、すべての期間は、それに応じて[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)になります。 
* *dim1*、 *dim2*、...: (省略可能) アクティビティメトリックスの計算をスライスするディメンション列の一覧です。

**戻り値**

各タイムライン期間と、既存の各ディメンションの組み合わせについて、個別のカウント値、個別の数、新しい値の数、保有率、およびチャーン率を含むテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*TimelineColumn*|dcount_values|dcount_newvalues|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|--|--|--|--|--|--|
|型: as of *TimelineColumn*|long|long|double|double|..|..|..|

**メモ**

***保有率の定義***

`Retention Rate`一定期間にわたって、次のように計算されます。

    # of customers returned during the period
    / (divided by)
    # customers at the beginning of the period

`# of customers returned during the period`は次のように定義されます。

    # of customers at end of period
    - (minus)
    # of new customers acquired during the period

`Retention Rate`0.0 から1.0 に変更できます。  
スコアが高いほど、より多くのユーザーを返すことになります。


***チャーン率の定義***

`Churn Rate`一定期間にわたって、次のように計算されます。
    
    # of customers lost in the period
    / (divided by)
    # of customers at the beginning of the period

`# of customer lost in the period`は次のように定義されます。

    # of customers at the beginning of the period
    - (minus)
    # of customers at the end of the period

`Churn Rate`0.0 から1.0 の範囲で、スコアが高いほど、より多くのユーザーがサービスに戻らないことを意味します。

***チャーンと保持率の比較***

およびの定義から派生した場合 `Churn Rate` `Retention Rate` 、常に次のようになります。

    [Retention rate] = 100.0% - [Churn Rate]


**使用例**

### <a name="weekly-retention-rate-and-churn-rate"></a>週単位のリテンション率とチャーン率

次のクエリでは、週の1週間分の期間のリテンション期間とチャーン率を計算します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2017-01-02 00:00: 00.0000000|(NaN)|(NaN)|
|2017-01-09 00:00: 00.0000000|0.179910044977511|0.820089955022489|
|2017-01-16 00:00: 00.0000000|0.744374437443744|0.255625562556256|
|2017-01-23 00:00: 00.0000000|0.612096774193548|0.387903225806452|
|2017-01-30 00:00: 00.0000000|0.681141439205955|0.318858560794045|
|2017-02-06 00:00: 00.0000000|0.278145695364238|0.721854304635762|
|2017-02-13 00:00: 00.0000000|0.223172628304821|0.776827371695179|
|2017-02-20 00:00: 00.0000000|0.38|0.62|
|2017-02-27 00:00: 00.0000000|0.295519001701645|0.704480998298355|
|2017-03-06 00:00: 00.0000000|0.280387770320656|0.719612229679344|
|2017-03-13 00:00: 00.0000000|0.360628154795289|0.639371845204711|
|2017-03-20 00:00: 00.0000000|0.288008028098344|0.711991971901656|
|2017-03-27 00:00: 00.0000000|0.306134969325153|0.693865030674847|
|2017-04-03 00:00: 00.0000000|0.356866537717602|0.643133462282398|
|2017-04-10 00:00: 00.0000000|0.495098039215686|0.504901960784314|
|2017-04-17 00:00: 00.0000000|0.198296836982968|0.801703163017032|
|2017-04-24 00:00: 00.0000000|0.0618811881188119|0.938118811881188|
|2017-05-01 00:00: 00.0000000|0.204657727593507|0.795342272406493|
|2017-05-08 00:00: 00.0000000|0.517391304347826|0.482608695652174|
|2017-05-15 00:00: 00.0000000|0.143667296786389|0.856332703213611|
|2017-05-22 00:00: 00.0000000|0.199122325836533|0.800877674163467|
|2017-05-29 00:00: 00.0000000|0.063468992248062|0.936531007751938|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-churn-and-retention.png" border="false" alt-text="アクティビティメトリックの変化とリテンション期間":::

### <a name="distinct-values-and-distinct-new-values"></a>個別の値と個別の ' new ' 値 

次のクエリは、個別の値と ' 新しい ' 値 (前の時間枠では表示されていない id) を週単位のウィンドウに対して計算します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2017-01-02 00:00: 00.0000000|630|630|
|2017-01-09 00:00: 00.0000000|738|575|
|2017-01-16 00:00: 00.0000000|1187|841|
|2017-01-23 00:00: 00.0000000|1092|465|
|2017-01-30 00:00: 00.0000000|1261|647|
|2017-02-06 00:00: 00.0000000|1744|1043|
|2017-02-13 00:00: 00.0000000|1563|432|
|2017-02-20 00:00: 00.0000000|1406|818|
|2017-02-27 00:00: 00.0000000|1956|1429|
|2017-03-06 00:00: 00.0000000|1593|848|
|2017-03-13 00:00: 00.0000000|1801|1423|
|2017-03-20 00:00: 00.0000000|1710|1017|
|2017-03-27 00:00: 00.0000000|1796|1516|
|2017-04-03 00:00: 00.0000000|1381|1008|
|2017-04-10 00:00: 00.0000000|1756|1162|
|2017-04-17 00:00: 00.0000000|1831|1409|
|2017-04-24 00:00: 00.0000000|1823|1164|
|2017-05-01 00:00: 00.0000000|1811|1353|
|2017-05-08 00:00: 00.0000000|1691|1246|
|2017-05-15 00:00: 00.0000000|1812|1608|
|2017-05-22 00:00: 00.0000000|1740|1017|
|2017-05-29 00:00: 00.0000000|960|756|

:::image type="content" source="images/activity-metrics-plugin/activity-metrics-dcount-and-dcount-newvalues.png" border="false" alt-text="アクティビティメトリックの dcount と dcount の新しい値":::
