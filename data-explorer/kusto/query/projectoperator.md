---
title: Project 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Project operator について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7a7cbb563a10b1cd1bdd91f12b0ce9d7da1c0e7b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346033"
---
# <a name="project-operator"></a>project 演算子

含める列、名前を変更する列、または削除する列を選択し、新しい計算列を挿入します。 

結果の列の順序は、引数の順序によって指定されます。 引数に指定された列だけが結果に含まれます。 入力内のその他の列は削除されます。  (逆の処理を実行する `extend`も組み合わせて使います)。

```kusto
T | project cost=price*quantity, price
```

## <a name="syntax"></a>構文

*T* `| project` *ColumnName* [ `=` *式*] [ `,` ...]
  
or
  
*T* `| project` [*columnname*  |  `(` *columnname*[ `,` ] `)` `=` ]*式*[ `,` ...]

## <a name="arguments"></a>引数

* *T*: 入力テーブル。
* *ColumnName:* 出力に表示される列の名前 (省略可能)。 *式*が存在しない場合は、 *ColumnName*が必須であり、その名前の列が入力に含まれている必要があります。 省略した場合、名前は自動的に生成されます。 *Expression*から複数の列が返される場合は、列名のリストをかっこで囲んで指定できます。 この場合、*式*の出力列には指定された名前が付けられ、残りの出力列はすべて削除されます。 列名のリストが指定されていない場合は、生成された名前を持つすべての*式*の出力列が出力に追加されます。
* *Expression:* 入力列を参照する、省略可能なスカラー式。 *ColumnName*が省略されていない場合、*式*は必須です。

    入力内の既存の列と同じ名前を持つ新しい計算列を返すことは、問題ありません。

## <a name="returns"></a>戻り値

引数で指定された列と、入力テーブルと同数の行が含まれるテーブル。

## <a name="example"></a>例

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