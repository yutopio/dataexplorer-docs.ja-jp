---
title: レンジ() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで range() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86558591e6312edd218230cda19a4afc17a13b27
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744583"
---
# <a name="range"></a>range()

一連の等間隔の値を保持する動的配列を生成します。

**構文**

`range(`*開始*`,`*停止*`,` [*ステップ*]`)` 

**引数**

* *start*: 結果の配列の最初の要素の値。 
* *stop*: 結果の配列の最後の要素の値、または結果の配列の最後の要素より大きい最小の値で、開始から*ステップ*の整数倍の中*に入ります*。
* *step*: 配列の連続する 2 つの要素の違い。 *ステップ*のデフォルト値は数値`1`および`1h``timespan``datetime`

**使用例**

次の例は、 `[1, 4, 7]`を返します。

```kusto
T | extend r = range(1, 8, 3)
```

次の例は、2015 年のすべての日を保持する配列を返します。

```kusto
T | extend r = range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

次の例は、 `[1,2,3]`を返します。

```kusto
range(1, 3)
```

次の例は、 `["01:00:00","02:00:00","03:00:00","04:00:00","05:00:00"]`を返します。

```kusto
range(1h, 5h)
```
