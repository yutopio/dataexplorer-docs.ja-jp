---
title: zip ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの zip () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 28fc477d4dfc5432434261e493f36985514ea28b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350555"
---
# <a name="zip"></a>zip()

関数は、 `zip` 任意の数の `dynamic` 配列を受け取り、同じインデックスの入力配列の要素を保持する配列を要素として持つ配列を返します。

## <a name="syntax"></a>構文

`zip(`*配列* `,` 1*配列 2*`, ... )`

## <a name="arguments"></a>引数

2 ~ 16 の動的配列。

## <a name="examples"></a>例

次の例は、 `[[1,2],[3,4],[5,6]]`を返します。

```kusto
print zip(dynamic([1,3,5]), dynamic([2,4,6]))
```

次の例は、 `[["A",{}], [1,"B"], [1.5, null]]`を返します。

```kusto
print zip(dynamic(["A", 1, 1.5]), dynamic([{}, "B"]))
```

次の例は、 `[[1,"one"],[2,"two"],[3,"three"]]`を返します。

```kusto
datatable(a:int, b:string) [1,"one",2,"two",3,"three"]
| summarize a = make_list(a), b = make_list(b)
| project zip(a, b)
```