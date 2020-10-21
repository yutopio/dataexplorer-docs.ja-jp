---
title: row_number ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの row_number () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 457e9445aa113e76052b9c4d96019352215d08f9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242808"
---
# <a name="row_number"></a>row_number()

シリアル化された [行セット](./windowsfunctions.md#serialized-row-set)内の現在の行のインデックスを返します。
行インデックスは、既定で最初の行のに対して開始され、 `1` `1` 追加の行ごとにによってインクリメントされます。
必要に応じて、行インデックスをとは異なる値で開始することもでき `1` ます。
また、指定された述語に従って行インデックスをリセットすることもできます。

## <a name="syntax"></a>構文

`row_number``(`[*StartingIndex* [ `,` *再起動*]]`)`

* *StartingIndex* は、 `long` 開始する行インデックスの値を示す型の定数式です (または、に再起動します)。 既定値は `1` です。
* *Restart* は、 `bool` 番号を *StartingIndex* 値に再起動するタイミングを示す型の省略可能な引数です。 指定しない場合、の既定値 `false` が使用されます。

## <a name="returns"></a>戻り値

関数は、現在の行の行インデックスを型の値として返し `long` ます。

## <a name="examples"></a>例

次の例では、2つの列を含むテーブルが返されます。1つ目の列 ( `a` ) の下の数値は `10` `1` です。2番目の列 () の数値は、 `rn` 次のよう `1` に `10` なります。

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

次の例は上記に似ていますが、2番目の列 () のみがから `rn` 始まり `7` ます。

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

最後の例では、データをパーティション分割し、各パーティションの行に番号を指定する方法を示します。 ここでは、次の方法でデータをパーティション分割し `Airport` ます。

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

空港  | 航空会社  | 離職  | 順位
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | LH       | 1           | 2
SEA      | LY       | 0           | 3
TLV      | LY       | 100         | 1
TLV      | LH       | 1           | 2