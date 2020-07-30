---
title: bin ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの bin () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 434d32a3b6597d71ea22c182a468d64d7971e6cb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348974"
---
# <a name="bin"></a>bin()

値を切り捨てて、指定された bin サイズの倍数である整数にします。 

と組み合わせて頻繁に使用され [`summarize by ...`](./summarizeoperator.md) ます。
値が分散している場合に、特定の値ごとの小さなセットにグループ化されます。

Null 値、ビンサイズが null の場合、またはビンサイズが負の場合は、null になります。 

エイリアスが `floor()` 機能します。

## <a name="syntax"></a>構文

`bin(`*値* `,`*roundTo*`)`

## <a name="arguments"></a>引数

* *値*: 数値、日付、または timespan。 
* *roundTo*: "bin サイズ"。 *value*を分割する数値、日付、または期間です。 

## <a name="returns"></a>戻り値

*value* 未満で、*roundTo* の最も近い倍数。  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>例

正規表現 | 結果
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


次の式は、バケットのサイズを 1 秒として、各期間のヒストグラムを計算します。

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```