---
title: column_ifexists() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでcolumn_ifexists() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4f7d4a2114aa2b9b3bb8ae3e951306a3ffb725e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517172"
---
# <a name="column_ifexists"></a>column_ifexists()

列名を文字列と既定値として使用します。 列への参照が存在する場合は、その列への参照を返します。

**構文**

`column_ifexists(`*列名*`, `*の既定値*)

**引数**

* *列名*: 列の名前
* *defaultValue*: 関数が使用されたコンテキストに列が存在しない場合に使用する値。
                  この値は、任意のスカラー式 (例えば、別の列への参照) にすることができます。

**戻り値**

*columnName が*存在する場合は、それが参照する列。 それ以外の場合 -*既定値 。*

**使用例**

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```