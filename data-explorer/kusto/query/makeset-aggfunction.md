---
title: make_set() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでmake_set() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: db11bd528703323d54ff96b228c0b80bbcb86dd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512684"
---
# <a name="make_set-aggregation-function"></a>make_set() (集計関数)

グループ内にある*式*で使用される個別の値セットの `dynamic` (JSON) 配列を返します 

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``make_set(`*エクスプル*`,` [*最大サイズ*]`)`

**引数**

* *Expr*: 集計計算の式。
* *MaxSize*は、返される要素の最大数に対するオプションの整数の制限です (既定値は*1048576)。* MaxSize 値は 1048576 を超えることはできません。

> [!NOTE]
> この関数の古いバージョンと古い`makeset()`バージョン: *MaxSize* = 128 の既定の制限があります。

**戻り値**

グループ内にある*式*で使用される個別の値セットの `dynamic` (JSON) 配列を返します 
配列の並べ替え順序が未定義です。

> [!TIP]
> 個別の値だけをカウントするには[、dcount() を使用します。](dcount-aggfunction.md)

**例**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

![alt text](./images/aggregations/makeset.png "makeset")

**参照**

* 反対[`mv-expand`](./mvexpandoperator.md)の関数には演算子を使用します。
* [`make_set_if`](./makesetif-aggfunction.md)演算子は、`make_set`に似ていますが、述語も受け入れます。