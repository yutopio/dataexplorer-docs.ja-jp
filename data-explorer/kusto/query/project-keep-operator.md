---
title: プロジェクト-keep operator-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのプロジェクト-keep 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/21/2020
ms.openlocfilehash: 2efc06ad701cc734ffad0bec7d89e3d3ef1d390d
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356724"
---
# <a name="project-keep-operator"></a>プロジェクト-keep 演算子

出力に保持する入力の列を選択します。

```kusto
T | project-keep price, quantity, zz*
```

結果の列の順序は、テーブル内の元の順序によって決まります。 引数として指定された列のみが保持されます。 その他の列は、結果から除外されます。 [`project`](projectoperator.md) も参照してください。

## <a name="syntax"></a>構文

*T* `| project-keep` *columnnameorpattern* [ `,` ...]

## <a name="arguments"></a>引数

* *T*: 入力テーブル
* *Columnnameorpattern:* 出力に保持する列または列のワイルドカードパターンの名前。

## <a name="returns"></a>戻り値

引数として指定された列を含むテーブル。 入力テーブルと同じ数の行が含まれています。

> [!TIP]
>* 列の名前を変更するには、を使用 [`project-rename`](projectrenameoperator.md) します。
>* 列の順序を変更するには、を使用 [`project-reorder`](projectreorderoperator.md) します。
>* 元の `project-keep` テーブルに存在する列、またはクエリの一部として計算された列を使用できます。

## <a name="example"></a>例

入力テーブル `T` には、`long` 型の列が 3 つあります (`A`、`B`、`C`)。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A1:long, A2:long, B:long) [1, 2, 3]
| project-keep A*    // Keeps only columns A1 and A2 in the output
```

|A1|A2|
|---|---|
|1|2|

## <a name="see-also"></a>関連項目

出力から除外する入力列を選択するには、[ [プロジェクト](projectawayoperator.md)] を使用します。
