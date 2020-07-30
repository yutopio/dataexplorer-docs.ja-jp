---
title: 範囲演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの範囲演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5c736492745d47428b5919d9791aa6115aaf8566
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345897"
---
# <a name="range-operator"></a>range 演算子

値が含まれる 1 列のテーブルを生成します。

パイプラインの入力はないことに注目してください。 

## <a name="syntax"></a>構文

`range`*columnName* `from`*開始* `to`*停止* `step`*ステップ*

## <a name="arguments"></a>引数

* *columnName*: 出力テーブル内の単一の列の名前。
* *start*: 出力の最小値。
* *stop*: 出力に生成される最大値 (または、*ステップ*がこの値をステップオーバーする場合は、最大値にバインドされている)。
* *ステップ*: 2 つの連続する値の差。 

この引数は、数値、日付、または期間の値である必要があります。 テーブルの列を参照することはできません (入力テーブルに基づいて範囲を計算する場合は、範囲関数を使用します。これは、おそらく mv-expand 演算子と同じです)。 

## <a name="returns"></a>戻り値

*ColumnName*という1つの列があり、値が*start*、 *start* `+` *step*、... であるテーブル。最大および終了まで*停止*します。

## <a name="example"></a>例  

過去 7 日間の深夜のテーブルです。 bin (floor) 関数により、各時刻が 1 日の開始時刻になっています。

<!-- csl: https://help.kusto.windows.net/Samples -->
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

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range Steps from 1 to 8 step 3
```

次の例では、演算子を使用して小規模なアドホックディメンションテーブルを作成する方法を示しています。このテーブルを使用して、 `range` ソースデータに値がないゼロが導入されます。

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
