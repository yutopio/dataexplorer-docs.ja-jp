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
ms.openlocfilehash: ffdd20c6d0c078cd3a7ecaa0d0fb2dac131ddda5
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2020
ms.locfileid: "91941776"
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

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="2つのテーブル。1つには仕入先、果物の種類、価格があり、いくつかの果物価格の組み合わせが繰り返されています。2番目のテーブルには、一意の組み合わせのみが表示されます。":::

**メモ**

* とは異なり、 `summarize by ...` 演算子は、 `distinct` アスタリスク ( `*` ) をグループキーとして指定することをサポートしているため、幅の広いテーブルでの使用が簡単になります。
* Group by キーの基数が高い場合は、 `summarize by ...` [シャッフル戦略](shufflequery.md) でを使用すると便利です。