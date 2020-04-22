---
title: series_seasonal() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_seasonal() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 559731ab7dca2e49e124a856ca7a66a912583b62
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663129"
---
# <a name="series_seasonal"></a>series_seasonal()

検出された季節期間または指定された季節期間に従って、系列の季節成分を計算します。

**構文**

`series_seasonal(`*シリーズ*`[,`*期間*`])`

**引数**

* *シリーズ*: 入力数値動的配列
* *期間*(オプション):各季節期間のビンの整数数、可能な値:
    *  -1 (デフォルト): しきい値*が0.7*の[series_periods_detect()](series-periods-detectfunction.md)を使用してピリオドを自動検出し、季節性が検出されない場合はゼロを返します。
    * 正の整数: 季節成分の期間として使用されます。
    * その他の値: 季節性を無視して一連のゼロを返す

**戻り値**

系列の計算された季節成分を含む*系列*入力と同じ長さの動的配列。 季節成分は、期間にわたるビンの位置に対応するすべての値の*中央値*として計算されます。

**以下も参照してください。**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**使用例**

**1. 期間を自動検出する**

次の例では、シリーズの期間が自動的に検出され、最初のシリーズの期間は6ビン、2番目の5ビンが検出され、第3系列の期間は短すぎて検出できないし、一連のゼロを返します(次の例を参照して、期間を強制する方法を参照)。

```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1]|[1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0]|
|[8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]|[10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0]|



**2. 強制的に期間を設定する**

次の例では、系列の期間が短すぎて[series_periods_detect()](series-periods-detectfunction.md)で検出できないので、期間を明示的に強制的に強制的に取得して季節パターンを取得します。

```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1,3,5,1,3,5,2,4,6]|[1.0,3.0,5.0,1.0,3.0,5.0,1.0,3.0,5.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5]|
