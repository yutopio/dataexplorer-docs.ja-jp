---
title: タイムスパン データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのタイムスパン データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31a0bfafed817ffaf531cffdcb844da8a357531f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509607"
---
# <a name="the-timespan-data-type"></a>タイムスパン データ型

( `timespan` `time`) データ型は、時間間隔を表します。

## <a name="timespan-literals"></a>タイムスパンリテラル

`timespan`型のリテラルには、次の`timespan(`表で示すように、value*に対*していくつかの形式がサポートされている構文*値*`)`があります。

|||
---|---
`2d`|2 日
`1.5h`|1.5 時間
`30m`|30 分
`10s`|10 秒
`0.1s`|0.1 秒
`100ms`| 100 ミリ秒
`10microsecond`|10マイクロ秒
`1tick`|100 ナノ秒
`time(15 seconds)`|15 秒
`time(2)`| 2 日
`time(0.12:34:56.7)`|`0d+12h+34m+56.7s`

特殊な形式`time(null)`は[null 値](null-values.md)です。

## <a name="timespan-operators"></a>タイムスパン演算子

型`timespan`の 2 つの値を加算、減算、および除算できます。
最後の操作は、一方の`real`値が他方に収まる小数の数を表す型の値を返します。

## <a name="examples"></a>例

次の例では、1 日の秒数をいくつかの方法で計算します。

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```