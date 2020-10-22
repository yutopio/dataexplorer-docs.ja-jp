---
title: プロジェクト外のオペレーター-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのプロジェクト外のオペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2938019237c882891af8ff86f4d33de3605a9063
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356522"
---
# <a name="project-away-operator"></a>project-away 演算子

出力から除外する入力の列を選択します。

```kusto
T | project-away price, quantity, zz*
```

結果の列の順序は、テーブル内の元の順序によって決まります。 引数として指定された列だけが削除されます。 その他の列は、結果に含まれます。 (逆の処理を実行する `project`も組み合わせて使います)。

## <a name="syntax"></a>構文

*T* `| project-away` *columnnameorpattern* [ `,` ...]

## <a name="arguments"></a>引数

* *T*: 入力テーブル
* *Columnnameorpattern:* 出力から削除する列または列のワイルドカードの名前。

## <a name="returns"></a>戻り値

引数として指定されていない列を含むテーブル。 入力テーブルと同じ数の行が含まれています。

> [!TIP]
>
> * 列の名前を変更するには、を使用 [`project-rename`](projectrenameoperator.md) します。
> * 列の順序を変更するには、を使用 [`project-reorder`](projectreorderoperator.md) します。
> * 元の `project-away` テーブルに存在する列、またはクエリの一部として計算された列を使用できます。

## <a name="examples"></a>例

入力テーブル `T` には、`long` 型の列が 3 つあります (`A`、`B`、`C`)。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|B|
|---|---|
|1|2|

' A ' で始まる列を削除しています。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a2='a2', b = 'b', a3='a3', a1='a1'
| project-away a*
```

|b|
|---|
|b|

## <a name="see-also"></a>関連項目

出力に保持する入力列を選択するには、 [プロジェクトの [保持](project-keep-operator.md)] を使用します。
