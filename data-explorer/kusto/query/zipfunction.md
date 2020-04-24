---
title: zip() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで zip() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd407ce652d41471be5b30a15c2c09b9f608edb1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504235"
---
# <a name="zip"></a>zip()

この`zip`関数は、任意の数`dynamic`の配列を受け取り、その要素がそれぞれ同じインデックスの入力配列の要素を保持する配列を返します。

**構文**

`zip(`*配列1*`,`*配列2*`, ... )`

**引数**

2 ~ 16 の動的配列。

**使用例**

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