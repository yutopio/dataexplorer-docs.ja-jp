---
title: プロジェクトの並べ替え操作-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのプロジェクトの並べ替え操作について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 74acab0dc4f0fbdaf7c77e609db3e41f875f2cad
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373148"
---
# <a name="project-reorder-operator"></a>project-reorder 演算子

結果の出力で列を並べ替えます。

```kusto
T | project-reorder Col2, Col1, Col* asc
```

**構文**

*T* `| project-reorder` *columnnameorpattern* [ `asc` | `desc` ] [ `,` ...]

**引数**

* *T*: 入力テーブル。
* *Columnnameorpattern:* 出力に追加された列または列のワイルドカードパターンの名前。
* ワイルドカードパターンの場合 `asc` : `desc` 列の名前を昇順または降順で指定したり、順序を指定したりします。 またはが指定されていない場合、 `asc` `desc` 順序は、ソーステーブルに出現する一致する列によって決定されます。

**戻り値**

演算子引数で指定された順序で列を含むテーブル。 `project-reorder`では、テーブルの列の名前を変更したり、列を削除したりすることはありません。そのため、ソーステーブルに存在していたすべての列が結果テーブルに表示されます。

**メモ**

- あいまいな*Columnnameorpattern*一致では、列はパターンに一致する最初の位置に表示されます。
- の列の指定 `project-reorder` は省略可能です。 明示的に指定されていない列は、出力テーブルの最後の列として表示されます。

* [`project-away`](projectawayoperator.md)列を削除するには、を使用します。
* [`project-rename`](projectrenameoperator.md)列の名前を変更するには、を使用します。


**使用例**

3つの列 (a、b、c) を使用してテーブルの順序を変更すると、2番目の列 (b) が最初に表示されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

で始まる列が `a` 他の列よりも前に表示されるように、テーブルの列の順序を変更します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|形式|a2|a3|b|
|---|---|---|---|
|形式|a2|a3|b|
