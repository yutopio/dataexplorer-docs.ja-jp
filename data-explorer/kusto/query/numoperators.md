---
title: 数値演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの数値演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6667005960152814be952d93fe932b4971d5322b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512021"
---
# <a name="numerical-operators"></a>数値演算子

型`int`、、`long`および`real`数値型を表します。
これらの型のペア間では、次の演算子を使用できます。

演算子       |説明                         |例
---------------|------------------------------------|-----------------------
`+`            |追加                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |減算                            |`0.23 - 0.22`,
`*`            |乗算                            |`1s * 5`, `2 * 2`
`/`            |Divide                              |`10m / 1s`, `4 / 2`
`%`            |剰余                              |`4 % 2`
`<`            |小さい                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |大きい                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |等しい                              |`1 == 1`
`!=`           |等しくない                          |`1 != 0`
`<=`           |以下                       |`4 <= 5`
`>=`           |以上                    |`5 >= 4`
`in`           |要素のいずれかに等しい       |[ここを参照してください。](inoperator.md)
`!in`          |要素のいずれとも等しくない   |[ここを参照してください。](inoperator.md)

**モジュロ演算子に関するコメント**

2 つの数値の剰余は常に Kusto に "小さな非負の数" を返します。
したがって、2つの数字のモジュロは *、ND、* % *D*次のようになります: &le; 0 ( &lt; *N* % *D*) 腹筋 (*D*).

たとえば、次のクエリがあります。

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

次の結果を生成します。

|プラスプラス  | マイナスプラス  | プラスマイナス  | マイナスマイナス|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |