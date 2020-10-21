---
title: percentile_tdigest ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの percentile_tdigest () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 7111f34fcd42e025b22960aa013310d7e4b672fd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252173"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

`tdigest`結果 ( [tdigest ()](tdigest-aggfunction.md)または[tdigest_merge ()](tdigest-merge-aggfunction.md)によって生成された) のパーセンタイルの結果を計算します

## <a name="syntax"></a>構文

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *typelの*場合]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*]...[ `,` *PercentileN*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*動的配列*`)`

## <a name="arguments"></a>引数

* *Expr*: [`tdigest`](tdigest-aggfunction.md) または [tdigest_merge ()](tdigest-merge-aggfunction.md)によって生成された式。
* *百分* 位は、百分位を指定する2つの定数です。
* *Typelo al*: 省略可能な型リテラル (たとえば、 `typeof(long)` )。 指定した場合、結果セットはこの型になります。 
* *Dynamic array*: 整数または浮動小数点数の動的配列内のパーセンタイルのリスト。

## <a name="returns"></a>戻り値

の各値のパーセンタイル/percentilesw 値 *`Expr`* 。

**ヒント**

* 関数は、少なくとも1パーセントを受け取る必要があります (さらに、上記の構文を参照してください: *Percentile1* [ `,` *Percentile2*]...[ `,` *PercentileN*]) を指定すると、結果を含む動的配列が生成されます。 (など [`percentiles()`](percentiles-aggfunction.md) )
  
* 1パーセントしか指定されていない場合、型も指定されていると、結果はそのパーセントの結果と同じ型の列になります。 この場合、すべて `tdigest` の関数がその型である必要があります。

* に *`Expr`* 異なる型の関数が含まれている場合は `tdigest` 、型を指定しないでください。 結果は dynamic 型になります。 次の例を参照してください。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|6200万|
|1億1000万|
|120万|
|250000|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0, 0, 0]|
|[0, 0, 62000000]|
|[0, 0, 110000000]|
|[0, 0, 1200000]|
|[0, 0, 250000]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[6200万]|
|["2007-12-20T11:30: 00.0000000 Z"]|
|["2007-12-31T23:59: 00.0000000 Z"]|
