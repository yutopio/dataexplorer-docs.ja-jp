---
title: series_seasonal ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_seasonal () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7997e6312f43316918c197c5ec10eec281495c63
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245921"
---
# <a name="series_seasonal"></a>series_seasonal()

検出または指定された季節期間に従って、系列の季節成分を計算します。

## <a name="syntax"></a>構文

`series_seasonal(`*シリーズ* `[,`*期間*`])`

## <a name="arguments"></a>引数

* *series*: 入力数値動的配列
* *period* (省略可能): 各季節期間の整数のビン数を指定します。可能な値は次のとおりです。
    *  -1 (既定値): しきい値が*0.7*の[series_periods_detect ()](series-periods-detectfunction.md)を使用して、自動検出します。 季節性が検出されない場合は0を返します
    * 正の整数: 季節コンポーネントの期間として使用されます。
    * その他の値: 季節性を無視し、一連のゼロを返します

## <a name="returns"></a>戻り値

系列の計算された季節成分を含む *系列* 入力と同じ長さの動的配列。 季節成分は、区間の位置に対応するすべての値の *中央* 値として計算されます。

## <a name="examples"></a>例

### <a name="auto-detect-the-period"></a>期間を自動検出する

次の例では、系列のピリオドが自動的に検出されます。 最初のシリーズの期間は6ビン、2つ目は5ビンとして検出されます。3番目の系列のピリオドは短すぎて検出されないため、一連のゼロを返します。 この [期間を強制する方法](#force-a-period)については、次の例を参照してください。

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

### <a name="force-a-period"></a>期間を強制する

この例では、系列のピリオドが短すぎて [series_periods_detect ()](series-periods-detectfunction.md)によって検出されないため、季節パターンを明示的に取得します。

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
 
## <a name="next-steps"></a>次のステップ

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)
