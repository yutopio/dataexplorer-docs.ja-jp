---
title: row_cumsum() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで row_cumsum() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92ebec75dcd7e44d59f964dc735e857f22f1ad00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510219"
---
# <a name="row_cumsum"></a>row_cumsum()

[シリアル化された行セット](./windowsfunctions.md#serialized-row-set)の列の累積合計を計算します。

**構文**

`row_cumsum``(`*用語*`,` [*再起動*]`)`

* *用語*は、合計する値を示す式です。
  式は`decimal`、 、 、 、`int`または`long``real`のいずれかの型のスカラーである必要があります。 Null*の用語*値は合計には影響しません。
* *[再起動*] は、累積`bool`操作を再開する (0 に戻す) タイミングを示す、オプションの型引数です。 データのパーティションを示すために使用できます。以下の 2 番目の例を参照してください。

**戻り値**

この関数は、引数の累積合計を返します。

**使用例**

次の例は、最初の数個の偶数の整数の累積合計を計算する方法を示しています。

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

この例では、データがパーティション分割されている場合の`salary`累積合計 ( ここでは ) を計算する方法`name`を示します ( ここで ) 。

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

name   | month  | 給与  | total
-------|--------|---------|------
Alice  | 1      | 1000    | 1000
Alice  | 2      | 2000    | 3000
Alice  | 3      | 1400    | 4400
Bob    | 1      | 1000    | 1000
Bob    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400