---
title: column_ifexists ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの column_ifexists () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5eeecf9e4756ac18cdeb5c6297aea1bcca5bac14
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348855"
---
# <a name="column_ifexists"></a>column_ifexists()

列名を文字列および既定値として取得します。 存在する場合は、その列への参照を返します。それ以外の場合は、既定値を返します。

## <a name="syntax"></a>構文

`column_ifexists(`*columnName* `, `*defaultValue*)

## <a name="arguments"></a>引数

* *columnName*: 列の名前
* *defaultValue*: 関数が使用されたコンテキストに列が存在しない場合に使用する値。
                  この値には、任意のスカラー式 (別の列への参照など) を指定できます。

## <a name="returns"></a>戻り値

*ColumnName*が存在する場合は、参照先の列です。 それ以外の場合は、 *defaultValue*。

## <a name="examples"></a>使用例

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```