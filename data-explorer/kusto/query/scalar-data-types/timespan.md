---
title: Timespan データ型-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの timespan データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 204076e8ed079dec69cae7080e7d2c50df52a9a6
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610323"
---
# <a name="the-timespan-data-type"></a>Timespan データ型

`timespan`( `time` ) データ型は、時間間隔を表します。

## <a name="timespan-literals"></a>timespan リテラル

型のリテラルには `timespan` `timespan(` *value* `)` 、次の表に示すように、*値*に対して複数の形式がサポートされている構文値があります。

|値|時間の長さ|
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

特殊な形式 `time(null)` は [null 値](null-values.md)です。

## <a name="timespan-operators"></a>timespan 演算子

型の2つの値を `timespan` 加算、減算、および除算できます。
最後の操作は、 `real` 1 つの値が他方の値に適合する回数を表す型の値を返します。

## <a name="examples"></a>例

次の例では、いくつかの方法で1日に含まれる秒数を計算します。

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```