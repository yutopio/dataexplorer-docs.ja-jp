---
title: percentile_tdigest() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで percentile_tdigest() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 655a0693b8e04b1230f6b9e8fe247bd2d77b7ac6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511239"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

消化不良の結果 (tdigest() または[tdigest_merge()](tdigest-merge-aggfunction.md)によって生成された) から百分位数の結果[を](tdigest-aggfunction.md)計算します。

**構文**

`percentile_tdigest(`*エクスプル*`,`*パーセンタイル1* [`,` *型リテラル*]`)`

`percentiles_array_tdigest(`*エクスプル*`,`*パーセンタイル1* [`,` *パーセンタイル2*] .[`,` *パーセンタイルルン*]`)`

`percentiles_array_tdigest(`*Expr* `,` *動的配列*`)`

**引数**

* *Expr*:[消化器](tdigest-aggfunction.md)または[tdigest_merge()](tdigest-merge-aggfunction.md)によって生成された式。
* *百分位数*は、百分位数を指定する二重定数です。
* *型リテラル*: 省略可能な型リテラル (たとえば`typeof(long)`、 ) 。 指定した場合、結果セットはこのタイプになります。 
* *動的配列*: 整数または浮動小数点数の動的配列内の百分位数のリスト

**戻り値**

*Expr*の各値の百分位/百分位数の値。

**ヒント**

1) 関数は少なくとも 1% を受け取る必要があります (そして、多分もっと、`,`上記の構文を参照してください: *Percentile1* [ *Percentile2*] .[`,` *パーセンタイルN*]結果は、結果を含む動的配列になります。 (など[`percentiles()`](percentiles-aggfunction.md))
  
2) 1% しか指定されず、タイプも指定された場合、結果はそのパーセントの結果と同じタイプの列になります (この場合、すべての消化器はそのタイプでなければなりません)。

3) *Expr*に異なるタイプの消化器が含まれている場合は、タイプを提供しないと結果は動的になります。 (下記の例を参照)。

**使用例**

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62000000|
|110000000|
|1200000|
|250000|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0,0,0]|
|[0,0,62000000]|
|[0,0,110000000]|
|[0,0,1200000]|
|[0,0,250000]|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62000000]|
|[2007-12-20T11:30:00.000000Z]|
|[2007-12-31T23:59:00.000000Z]|