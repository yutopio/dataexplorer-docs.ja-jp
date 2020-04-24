---
title: プロジェクトオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのプロジェクト オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de13c240180d00b82736a0dd35cb83c08639682f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510933"
---
# <a name="project-operator"></a>プロジェクト演算子

含める列、名前を変更する列、または削除する列を選択し、新しい計算列を挿入します。 

結果の列の順序は、引数の順序によって指定されます。 引数に指定された列のみが結果に含まれます。 入力内の他の列は削除されます。  (逆の処理を実行する `extend`も組み合わせて使います)。

```kusto
T | project cost=price*quantity, price
```

**構文**

*T* `| project` *列名*[`=` `,`*式*] [ ..]
  
or
  
*T* `| project` [*列名* | `(``,`*列名*]`,` [ ]`)` `=`*式*[ .]

**引数**

* *T*: 入力テーブル。
* *列名:* 出力に表示する列の名前 (省略可能)。 *式*がない場合 *、ColumnName*は必須であり、その名前の列が入力に含まれている必要があります。 省略すると、名前が自動的に生成されます。 *Expression*が複数の列を返す場合は、列名のリストをかっこで囲んで指定できます。 この場合 *、Expression*の出力列には指定された名前が付けられます。 列名のリストが指定されていない場合、生成された名前を持つ*すべての式*の出力列が出力に追加されます。
* *Expression:* 入力列を参照する、省略可能なスカラー式。 *列名*を省略しない場合は *、式*は必須です。

    入力内の既存の列と同じ名前を持つ新しい計算列を返すことは、問題ありません。

**戻り値**

引数で指定された列と、入力テーブルと同数の行が含まれるテーブル。

**例**

次の例は、 `project` 演算子を使って実行できる何種類かの操作を示しています。 入力テーブル `T` には、`int` 型の列が 3 つあります (`A`、`B`、`C`)。 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md)は、複数の列を返す関数の例です。