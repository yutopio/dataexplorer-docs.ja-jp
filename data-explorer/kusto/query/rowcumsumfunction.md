---
title: row_cumsum ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの row_cumsum () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83dc48589fce7332c8e24d1e5a47c75a6cfca608
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345727"
---
# <a name="row_cumsum"></a>row_cumsum()

シリアル化された[行セット](./windowsfunctions.md#serialized-row-set)内の列の累積合計を計算します。

## <a name="syntax"></a>構文

`row_cumsum``(`*用語*[ `,` *再起動*]`)`

* *Term*は、合計する値を示す式です。
  式は、、、 `decimal` 、またはのいずれかの型のスカラーである必要があり `int` `long` `real` ます。 Null*用語*の値は、合計には影響しません。
* *Restart*は `bool` 、累積操作を再起動するタイミング (0 に戻す) を示す型の省略可能な引数です。 データのパーティションを示すために使用できます。次の2番目の例を参照してください。

## <a name="returns"></a>戻り値

関数は、引数の累積合計を返します。

## <a name="examples"></a>例

次の例では、最初のいくつかの整数の累積合計を計算する方法を示します。

```kusto
datatable (a:long) [
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10
]
| where a%2==0
| serialize cs=row_cumsum(a)
```

a    | cs
-----|-----
2    | 2
4    | 6
6    | 12
8    | 20
10   | 30

この例では、 `salary` データがパーティション分割されたときの累積合計 (ここでは) を計算する方法を示します (ここでは `name` )。

```kusto
datatable (name:string, month:int, salary:long)
[
    "Alice", 1, 1000,
    "Bob",   1, 1000,
    "Alice", 2, 2000,
    "Bob",   2, 1950,
    "Alice", 3, 1400,
    "Bob",   3, 1450,
]
| order by name asc, month asc
| extend total=row_cumsum(salary, name != prev(name))
```

name   | month  | 給  | total
-------|--------|---------|------
Alice  | 1      | 1000    | 1000
Alice  | 2      | 2000    | 3000
Alice  | 3      | 1400    | 4400
Bob    | 1      | 1000    | 1000
Bob    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400