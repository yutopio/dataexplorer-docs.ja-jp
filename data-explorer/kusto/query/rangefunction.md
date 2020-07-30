---
title: range ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの範囲 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2606746e89d645601fa53ed7f81d67ddae203c03
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345914"
---
# <a name="range"></a>range()

等間隔に並んだ一連の値を保持する動的配列を生成します。

## <a name="syntax"></a>構文

`range(`*開始* `,`*停止*[ `,` *ステップ*]`)` 

## <a name="arguments"></a>引数

* *start*: 結果として得られる配列内の最初の要素の値。 
* *stop*: 結果として得られる配列内の最後の要素の値、または結果の配列内の最後の要素より大きい最小値、および*開始*からの*ステップ*の整数倍の値。
* *step*: 配列の2つの連続する要素の差。 *ステップ*の既定値は、 `1` 数値の場合は、 `1h` `timespan` またはの場合はです。`datetime`

## <a name="examples"></a>例

次の例は、 `[1, 4, 7]`を返します。

```kusto
T | extend r = range(1, 8, 3)
```

次の例は、2015 年のすべての日を保持する配列を返します。

```kusto
T | extend r = range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

次の例は、 `[1,2,3]`を返します。

```kusto
range(1, 3)
```

次の例は、 `["01:00:00","02:00:00","03:00:00","04:00:00","05:00:00"]`を返します。

```kusto
range(1h, 5h)
```
