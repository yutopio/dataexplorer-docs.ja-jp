---
title: プロジェクトの並べ替え演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのプロジェクトの並べ替え演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf56a69891f83aaabc12dbbcd8f758ecb963b493
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510848"
---
# <a name="project-reorder-operator"></a>project-reorder 演算子

結果出力の列の順序を変更します。

```kusto
T | project-reorder Col2, Col1, Col* asc
```

**構文**

*T* `| project-reorder` *列名またはパターン*[`asc`|`desc`] [ .]`,`

**引数**

* *T*: 入力テーブル。
* *列名またはパターン:* 出力に追加される列または列のワイルドカード パターンの名前。
* ワイルドカード パターンの場合: `asc` `desc`列の名前を昇順または降順で指定または並べ替えます。 指定`asc``desc`されていない場合、順序は、ソース テーブルに表示される一致する列によって決まります。

**戻り値**

演算子の引数で指定された順序で列を含むテーブル。 `project-reorder`では、テーブルの名前変更や列の削除は行わないため、ソース テーブルに存在するすべての列が結果テーブルに表示されます。

**メモ**

- あいまいな*列名またはパターン*一致では、列はパターンに一致する最初の位置に表示されます。
- の列の指定`project-reorder`はオプションです。 明示的に指定されていない列は、出力テーブルの最後の列として表示されます。

* 列[`project-away`](projectawayoperator.md)を削除するために使用します。
* 列[`project-rename`](projectrenameoperator.md)の名前を変更するために使用します。


**使用例**

3 つの列 (a,b, c) を持つ表を並べ替え、2 番目の列 (b) が最初に表示されるようにします。

```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

テーブルの列を並べ替え、先頭の`a`列が他の列の前に表示されるようにします。

```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|a1|a2|a3|b|
|---|---|---|---|
|a1|a2|a3|b|