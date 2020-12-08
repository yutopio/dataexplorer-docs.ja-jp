---
title: project 演算子 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の project 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: f94003573cab076d8fa83537cb7868e21b9b084c
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513269"
---
# <a name="project-operator"></a>project 演算子

含める列、名前を変更する列、または削除する列を選択し、新しい計算列を挿入します。 

結果の列の順序は、引数の順序によって指定されます。 結果には、引数で指定された列のみが含まれます。 入力内のその他の列は削除されます  (`extend` も参照してください)。

```kusto
T | project cost=price*quantity, price
```

## <a name="syntax"></a>構文

*T* `| project` *ColumnName* [`=` *Expression*] [`,` ...]
  
または
  
*T* `| project` [*ColumnName* | `(`*ColumnName*[`,`]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>引数

* *T*: 入力テーブル。
* *ColumnName:* 出力に表示される列の省略可能な名前。 *Expression* を指定しない場合、*ColumnName* は必須であり、この名前の列が入力に存在する必要があります。 省略した場合、名前は自動的に生成されます。 "*式*" によって複数の列が返される場合は、かっこで囲んで列名のリストを指定できます。 この場合、*Expression* の出力列には指定した名前が設定され、それ以外の出力列は削除されます (存在する場合)。 列名のリストを指定しない場合、*Expression* のすべての出力列の名前が生成され、出力に追加されます。
* *Expression:* 入力列を参照する、省略可能なスカラー式。 *ColumnName* が省略されていない場合は *Expression* が必須です。

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

複数の列を返す関数の例として [series_stats](series-statsfunction.md) があります。