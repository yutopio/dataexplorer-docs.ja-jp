---
title: serialize 操作-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの serialize 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 708a5ccd5f8402dedb074a6ab8c17b1d7762839c
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717123"
---
# <a name="serialize-operator"></a>serialize 演算子

入力行セットの順序を、ウィンドウ関数に対して安全に使用できることを示します。

演算子は、宣言的な意味を持ちます。 入力行セットをシリアル化 (順序付け) としてマークし、[ウィンドウ関数](./windowsfunctions.md)を適用できるようにします。

```kusto
T | serialize rn=row_number()
```

**構文**

`serialize`[*Name1* `=`*Expr1 or* [ `,` *Name2* `=` *Expr2*]...]

* *名前*の / *Expr*のペアは、 [extend 演算子](./extendoperator.md)の組に似ています。

**例**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

次の演算子の出力行セットは、シリアル化としてマークされます。

[range](./rangeoperator.md)、 [sort](./sortoperator.md)、 [order](./orderoperator.md)、 [top](./topoperator.md)、 [top-hitters](./tophittersoperator.md)、 [getschema](./getschemaoperator.md)。

次の演算子の出力行セットは、非シリアル化としてマークされます。

[sample](./sampleoperator.md)、 [sample-distinct](./sampledistinctoperator.md)、 [distinct](./distinctoperator.md)、 [join](./joinoperator.md)、 [top nested](./topnestedoperator.md)、 [count](./countoperator.md)、[要約](./summarizeoperator.md)、[ファセット](./facetoperator.md)、 [mv-展開](./mvexpandoperator.md)、[評価](./evaluateoperator.md)、[縮小](./reduceoperator.md)、および[シリーズ](./make-seriesoperator.md)

その他のすべての演算子は、シリアル化プロパティを保持します。 入力行セットがシリアル化されている場合は、出力行セットもシリアル化されます。
