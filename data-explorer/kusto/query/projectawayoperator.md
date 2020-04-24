---
title: プロジェクトアウェイオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのプロジェクト アウェイ オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 38ec57e9659458ef34117e4a380c756310db218b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510950"
---
# <a name="project-away-operator"></a>project-away 演算子

出力から除外する入力列を選択します。

```kusto
T | project-away price, quantity, zz*
```

結果の列の順序は、表の元の順序によって決まります。 引数として指定された列だけが削除されます。 他の列は結果に含まれます。  (逆の処理を実行する `project`も組み合わせて使います)。

**構文**

*T* `| project-away` *列名またはパターン*[`,` .]

**引数**

* *T*: 入力テーブル
* *列名またはパターン:* 出力から削除する列または列のワイルドカード パターンの名前。

**戻り値**

引数として名前が付けられていない列を持つテーブル。 入力テーブルと同じ行数を含みます。

**ヒント**

* 列[`project-rename`](projectrenameoperator.md)の名前を変更する場合に使用します。
* 列[`project-reorder`](projectreorderoperator.md)の順序を変更する場合に使用します。

* 元の`project-away`テーブルに存在する列、またはクエリの一部として計算された任意の列を使用できます。


**使用例**

入力テーブル `T` には、`long` 型の列が 3 つあります (`A`、`B`、`C`)。

```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|B|
|---|---|
|1|2|

'a' で始まる列を削除しています。

```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

