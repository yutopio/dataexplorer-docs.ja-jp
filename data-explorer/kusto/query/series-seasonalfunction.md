---
title: series_seasonal ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_seasonal () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 88160a55ba8342e3ed6bce90ec77c5a370ab7358
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372465"
---
# <a name="series_seasonal"></a>series_seasonal()

検出または指定された季節期間に従って、系列の季節成分を計算します。

**構文**

`series_seasonal(`*シリーズ* `[,`*期間*`])`

**引数**

* *series*: 入力数値動的配列
* *period* (省略可能): 各季節期間の整数のビン数を指定します。可能な値は次のとおりです。
    *  -1 (既定値): しきい値が*0.7*の[series_periods_detect ()](series-periods-detectfunction.md)を使用して期間を自動検出します。季節性が検出されない場合は0を返します
    * 正の整数: 季節コンポーネントの期間として使用されます
    * その他の値: 季節性を無視し、一連のゼロを返します。

**戻り値**

系列の計算された季節成分を含む*系列*入力と同じ長さの動的配列。 季節成分は、期間におけるビンの位置に対応するすべての値の*中央*値として計算されます。

**関連項目:**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**使用例**

**1. 期間を自動検出します**

次の例では、系列の期間が自動的に検出され、最初の系列の期間が6ビン、2番目の5ビン、3番目のシリーズの期間が短すぎて検出されないため、一連のゼロが返されます (期間を強制する方法については次の例を参照してください)。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2、5、3、4、3、2、1、2、3、4、3、2、1、2、3、4、3、2、1、2、3、4、3、2、1]|[1.0、2.0、3.0、4.0、3.0、2.0、1.0、2.0、3.0、4.0、3.0、2.0、1.0、2.0、3.0、4.0、3.0、2.0、1.0、2.0、3.0、4.0、3.0、2.0、1.0]|
|[8、12、14、12、10、10、12、14、12、10、10、12、14、12、10、10、12、14、12、10]|[10.0、12.0、14.0、12.0、10.0、10.0、12.0、14.0、12.0、10.0、10.0、12.0、14.0、12.0、10.0、10.0、12.0、14.0、12.0、10.0]|
|[1、3、5、2、4、6、1、3、5、2、4、6]|[0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0、0.0]|



**2. 強制的にピリオドを強制する**

次の例では、系列のピリオドが短すぎて[series_periods_detect ()](series-periods-detectfunction.md)によって検出されないため、季節を明示的に季節パターンを取得します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1、3、5、1、3、5、2、4、6]|[1.0、3.0、5.0、1.0、3.0、5.0、1.0、3.0、5.0]|
|[1、3、5、2、4、6、1、3、5、2、4、6]|[1.5、3.5、5.5、1.5、3.5、5.5、1.5、3.5、5.5、1.5、3.5、5.5]|
