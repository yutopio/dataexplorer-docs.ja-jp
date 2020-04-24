---
title: row_number() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで row_number() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8cb01ed098d24632154215ddf06dc2ab1d72695
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510168"
---
# <a name="row_number"></a>row_number()

[シリアル化された行セット](./windowsfunctions.md#serialized-row-set)の現在の行のインデックスを返します。
行インデックスは、最初の行`1`の既定で開始され、追加の行`1`ごとに増分されます。
必要に応じて、行インデックスは、 と`1`は異なる値で開始できます。
さらに、行インデックスは、指定された述語に従ってリセットされる場合があります。

**構文**

`row_number``(` [*インデックスの開始*]`,` *[ 再起動*] ]`)`

* *StartingIndex*は、開始位置`long`(または再開する行インデックス) の値を示す型の定数式です。 既定値は `1` です。
* *[再開*] は、番号`bool`付けを*開始*インデックス値に再開するタイミングを示す、省略可能な型の引数です。 指定しない場合は、デフォルト値が`false`使用されます。

**戻り値**

この関数は、現在の行の行インデックスを type の`long`値として返します。

**使用例**

次の例では、2 つの列を持つテーブル、`a`最初の列`10`( `1`) に下から`rn`から、 までの`1`数値を`10`含む 2 番目の列 ( ) を返します。

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

次の例は上記と同様で、2 番目の列`rn`( `7`) だけが で始まります。

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

最後の例では、データをパーティション分割し、各パーティションごとに行に番号を付けることができる方法を示します。 ここでは、データをパーティション分割`Airport`します。

```kusto
datatable (Airport:string, Airline:string, Departures:long)
[
  "TLV", "LH", 1,
  "TLV", "LY", 100,
  "SEA", "LH", 1,
  "SEA", "BA", 2,
  "SEA", "LY", 0
]
| sort by Airport asc, Departures desc
| extend Rank=row_number(1, prev(Airport) != Airport)
```

このクエリを実行すると、次の結果が生成されます。

空港  | 航空会社  | 出発  | Rank
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | Lh       | 1           | 2
SEA      | LY       | 0           | 3
Tlv      | LY       | 100         | 1
Tlv      | Lh       | 1           | 2