---
title: series_dot_product_fl ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_dot_product_fl () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: 9065c6b86807ad27c588ebffe3334c450a1addcb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321847"
---
# <a name="series_dot_product_fl"></a>series_dot_product_fl()

2つの数値ベクトルのドット積を計算します。

関数は、 `series_dot_product_fl()` 2 つの動的数値配列を含む式を入力として受け取り、その [ドット積](https://en.wikipedia.org/wiki/Dot_product)を計算します。

> [!NOTE]
> この関数は、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`series_dot_product_fl(`*vec1* `,`*vec2*`)`
  
## <a name="arguments"></a>引数

* *vec1*: 数値の動的配列セル。
* *vec2*: 数値の動的配列セル。 *vec1* と同じ長さです。

## <a name="usage"></a>使用

`series_dot_product_fl()` はユーザー定義関数です。 クエリにコードを埋め込むか、データベースにインストールすることができます。 使用方法には、アドホックと永続的な2つの方法があります。 例については、以下のタブを参照してください。

# <a name="ad-hoc"></a>[アドホック](#tab/adhoc)

アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_dot_product_fl=(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
};
//
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

# <a name="persistent"></a>[永続的](#tab/persistent)

永続的な使用方法については、を使用 [`.create function`](../management/create-function.md) します。 関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="one-time-installation"></a>1 回限りのインストール

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate the dot product of 2 numerical arrays")
series_dot_product_fl(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
}
```

### <a name="usage"></a>使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

---

:::image type="content" source="images/series-dot-product-fl/dot-product-result.png" alt-text="Azure データエクスプローラーのユーザー定義関数 series_dot_product_fl を使用した2つのベクトルのドット積の結果を示す表" border="false":::
