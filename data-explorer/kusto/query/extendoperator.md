---
title: 拡張演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの拡張演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32100f6668c2fb20ae715b985b0bf3612e13e69b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348158"
---
# <a name="extend-operator"></a>extend 演算子

計算列を作成し、結果セットに追加します。

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>構文

*T* `| extend` [*columnname*  |  `(` *columnname*[ `,` ...] `)` `=` ]*式*[ `,` ...]

## <a name="arguments"></a>引数

* *T*: 入力表形式の結果セット。
* *ColumnName:* Optional. 追加または更新する列の名前。 省略した場合、名前が生成されます。 *Expression*から複数の列が返される場合は、列名のリストをかっこで囲んで指定できます。 この場合、*式*の出力列には指定された名前が付けられ、残りの出力列は削除されます。 列名の一覧が指定されていない場合、生成された名前を持つすべての*式*の出力列が出力に追加されます。
* *式:* 入力の列に対する計算。

## <a name="returns"></a>戻り値

入力の表形式の結果セットのコピー。次に例を示します。
1. `extend`入力に既に存在すると示されている列名は、新しい計算値として削除され、追加されます。
2. によって示されている列名 `extend` は、入力に存在しないため、新しい計算値として追加されます。

**ヒント**

* 操作は、 `extend` インデックスを持た**ない**入力結果セットに新しい列を追加します。 ほとんどの場合、新しい列が、インデックスを持つ既存のテーブル列とまったく同じになるように設定されている場合、Kusto では既存のインデックスを自動的に使用できます。 ただし、複雑なシナリオでは、この伝達は行われません。 このような場合、列の名前を変更することが目的である場合は、代わりに[ `project-rename` 演算子](projectrenameoperator.md)を使用します。

## <a name="example"></a>例

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

[Series_stats](series-statsfunction.md)関数を使用すると、複数の列を返すことができます。