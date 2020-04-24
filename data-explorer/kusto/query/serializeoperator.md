---
title: シリアル化演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのシリアル化演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5402d1e1fcceb42f02643bf24918ed07beddaed7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509114"
---
# <a name="serialize-operator"></a>serialize 演算子

ウィンドウ関数の使用に対して入力行セットの順序が安全であることを示します。

演算子は宣言的な意味を持ち、入力行セットをシリアル化 (順序付き) としてマークして[、ウィンドウ関数](./windowsfunctions.md)を適用できるようにします。

```kusto
T | serialize rn=row_number()
```

**構文**

`serialize`[*名前 1* `=` *エクスプル 1* [`,`名前*2* `=` *Expr2*].

* *名前*/*Expr*ペアは[、拡張演算子](./extendoperator.md)のペアと似ています。

**例**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

次の演算子の出力行セットは、シリアル化済みとしてマークされます。

[範囲](./rangeoperator.md),[並べ替え](./sortoperator.md),[順序](./orderoperator.md),[トップ](./topoperator.md),[トップヒッター ,](./tophittersoperator.md)[ゲットスキーマ](./getschemaoperator.md).

次の演算子の出力行セットは、シリアル化されていないとしてマークされます。

[サンプル](./sampleoperator.md),[サンプル別 ,](./sampledistinctoperator.md)[個別](./distinctoperator.md)の ,[結合](./joinoperator.md),[トップネスト](./topnestedoperator.md),[カウント](./countoperator.md),[集計](./summarizeoperator.md),[ファセット](./facetoperator.md), [mv-expand](./mvexpandoperator.md),[評価](./evaluateoperator.md)[,](./reduceoperator.md)メイク[シリーズ](./make-seriesoperator.md)

他のすべての演算子は、シリアル化プロパティを保持します (入力行セットがシリアル化されている場合は、出力行セットも同じになります)。