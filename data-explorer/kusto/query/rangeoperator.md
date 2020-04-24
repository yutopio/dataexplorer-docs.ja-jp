---
title: 範囲演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの範囲演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7ec559a23e28568ce7abd8365cdc502ad05a9b0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510610"
---
# <a name="range-operator"></a>range 演算子

値が含まれる 1 列のテーブルを生成します。

パイプラインの入力はないことに注目してください。 

**構文**

`range`*列名前開始*`from`*start*`to`*停止**step*ステップ`step`

**引数**

* *columnName*: 出力テーブル内の単一列の名前。
* *start*: 出力の最小値。
* *stop*: 出力で生成される最高値 (または *、この*値をステップステップがステップオーバーする場合は、最高値にバインドされている値)。
* *step*: 2 つの連続した値の差です。 

この引数は、数値、日付、または期間の値である必要があります。 テーブルの列を参照することはできません (入力テーブルに基づいて範囲を計算する場合は、おそらく mv-expand 演算子を使用して範囲関数を使用します。 

**戻り値**

*columnName*という単一の列を持つテーブルで、値*start*`+`は*開始*、*開始ステップ*、..まで、そして*停止*するまで.

**例**  

過去 7 日間の深夜のテーブルです。 bin (floor) 関数により、各時刻が 1 日の開始時刻になっています。

```kusto
range LastWeek from ago(7d) to now() step 1d
```

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|


`Steps` という 1 つの列を持つテーブルです。その列の型は `long`、値は `1`、`4`、`7` です。

```kusto
range Steps from 1 to 8 step 3
```

次の例では、演算子`range`を使用して、ソース データに値がない場合にゼロを導入するために使用される、小規模なアドホック ディメンション テーブルを作成する方法を示します。

```kusto
range TIMESTAMP from ago(4h) to now() step 1m
| join kind=fullouter
  (Traces
      | where TIMESTAMP > ago(4h)
      | summarize Count=count() by bin(TIMESTAMP, 1m)
  ) on TIMESTAMP
| project Count=iff(isnull(Count), 0, Count), TIMESTAMP
| render timechart  
```