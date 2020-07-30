---
title: distinct 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの distinct 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 233d3fdb0e25720b860a0c11515daec7c597dadd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348345"
---
# <a name="distinct-operator"></a>distinct 演算子

入力テーブルの指定された列の個別の組み合わせを含むテーブルを生成します。 

```kusto
T | distinct Column1, Column2
```

入力テーブル内のすべての列の個別の組み合わせを含むテーブルを生成します。

```kusto
T | distinct *
```

## <a name="example"></a>例

果物と価格の個別の組み合わせを示します。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Distinct":::

**メモ**

* とは異なり、 `summarize by ...` 演算子は、 `distinct` アスタリスク ( `*` ) をグループキーとして指定することをサポートしているため、幅の広いテーブルでの使用が簡単になります。
* Group by キーの基数が高い場合は、 `summarize by ...` [シャッフル戦略](shufflequery.md)でを使用すると便利です。