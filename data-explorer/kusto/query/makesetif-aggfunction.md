---
title: make_set_if() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの make_set_if() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1393e063fb0abb91b38a8b9e1edc0110e78b3638
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512650"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if() (集計関数)

`dynamic` *Expr*がグループ内で取り込む一連*Expr*の個別値の配列 (JSON) を返します`true`。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``make_set_if(`*エクスプル*,*述語*[`,` *最大サイズ*]`)`

**引数**

* *Expr*: 集計の計算に使用する式。
* *述語*: 結果に追加`true`する*Expr*に対して評価する必要がある述部。
* *MaxSize*は、返される要素の最大数に対するオプションの整数の制限です (既定値は*1048576)。* MaxSize 値は 1048576 を超えることはできません。

**戻り値**

`dynamic` *Expr*がグループ内で取り込む一連*Expr*の個別値の配列 (JSON) を返します`true`。
配列の並べ替え順序が未定義です。

> [!TIP]
> 個別の値のみをカウントするには[、dcountif() を](dcountif-aggfunction.md)使用します。

**参照**

[`make_set`](./makeset-aggfunction.md)述語式なしで同じことを行う関数。

**例**

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|[ジョージ、リンゴ]|