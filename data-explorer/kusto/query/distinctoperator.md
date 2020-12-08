---
title: distinct 演算子 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の distinct 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513167"
---
# <a name="distinct-operator"></a>distinct 演算子

入力テーブルの指定された列の個別の組み合わせを含むテーブルを生成します。 

```kusto
T | distinct Column1, Column2
```

入力テーブルのすべての列の個別の組み合わせを含むテーブルを生成します。

```kusto
T | distinct *
```

## <a name="example"></a>例

果物と価格の個別の組み合わせを示します。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="2 つのテーブル。1 つには仕入先、果物の種類、および価格があり、いくつかの果物と価格の組み合わせが繰り返されています。2 番目のテーブルには、一意の組み合わせのみが示されています。":::

**メモ**

* `summarize by ...` とは異なり、`distinct` 演算子では、アスタリスク (`*`) をグループ キーとして指定することがサポートされているため、幅の広いテーブルで簡単に使用できます。
* キー別のグループのカーディナリティが高い場合、`summarize by ...` と[シャッフル戦略](shufflequery.md)を使用すると役に立つ可能性があります。